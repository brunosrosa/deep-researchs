---
aliases: []
sticker: lucide//chevron-right-square
---
# Genesis MC: Guia Prático - Parte 3 (O "Palco" Multimodal)

O chat linear morreu. O **SODA (Sovereign OS)** utiliza um Canvas Espacial para interação. Isso reduz drasticamente a carga cognitiva, permitindo que você (o humano) veja a árvore de decisão do agente e expanda apenas as partes que importam.

Este guia foca na implementação com **Xyflow (React Flow)**, a escolha arquitetural ideal para mapear o _Sequential Thinking MCP_ e o raciocínio passo-a-passo dos seus SLMs locais.

## 1. Setup do Ecossistema React + Xyflow no Tauri

Assumindo que o seu frontend (dentro do Tauri) está usando Vite + React + TypeScript.

**Instalação das dependências essenciais:**

```
# Dentro da pasta do seu frontend (ex: src/)
npm install @xyflow/react @tauri-apps/api lucide-react clsx tailwind-merge
```

**Por que Xyflow e não Tldraw aqui?**

Ambos têm nota 10 na sua planilha, mas servem a propósitos distintos no SODA:

- **Xyflow:** Para a Mente do Orquestrador (visualizar nós de raciocínio, chamadas de ferramentas MCP, fluxos de dados). **Usaremos este agora.**
- **Tldraw:** Para o "Quadro Branco" (desenho livre, vibe coding, sketch-to-code). Ficará como uma aba ou modo alternativo.

## 2. A Ponte: Escutando o Motor Rust (`useAgentState.ts`)

Na Parte 2, fizemos o Rust emitir eventos `agent-thought`. Agora precisamos de um Hook React que escute esses eventos e os transforme em Nós (Nodes) e Arestas (Edges) no Canvas em tempo real.

```ts
// src/hooks/useAgentState.ts
import { useState, useEffect, useCallback } from 'react';
import { listen } from '@tauri-apps/api/event';
import { Node, Edge, addEdge, applyNodeChanges, applyEdgeChanges, NodeChange, EdgeChange } from '@xyflow/react';

export type AgentThoughtPayload = {
  id: string;
  agent_id: string;
  content: string;
  is_final: bool;
  tool_called?: string; // Ex: "Time_MCP"
  parent_id?: string;   // Para ligar o nó atual ao anterior
};

export function useAgentState() {
  const [nodes, setNodes] = useState<Node[]>([]);
  const [edges, setEdges] = useState<Edge[]>([]);

  const onNodesChange = useCallback(
    (changes: NodeChange[]) => setNodes((nds) => applyNodeChanges(changes, nds)),
    []
  );
  
  const onEdgesChange = useCallback(
    (changes: EdgeChange[]) => setEdges((eds) => applyEdgeChanges(changes, eds)),
    []
  );

  useEffect(() => {
    // Escuta o evento que vem do backend em Rust
    const unlisten = listen<AgentThoughtPayload>('agent-thought', (event) => {
      const payload = event.payload;

      setNodes((prevNodes) => {
        // Se o nó já existe (ex: streaming de tokens), atualiza o conteúdo
        const existingNodeIndex = prevNodes.findIndex(n => n.id === payload.id);
        
        if (existingNodeIndex !== -1) {
          const updatedNodes = [...prevNodes];
          updatedNodes[existingNodeIndex].data = {
            ...updatedNodes[existingNodeIndex].data,
            content: payload.content,
            isFinal: payload.is_final
          };
          return updatedNodes;
        }

        // Se é um pensamento novo, cria um novo nó (Progressive Disclosure Node)
        const newNode: Node = {
          id: payload.id,
          type: 'progressiveThought', // Nosso nó customizado
          position: { x: prevNodes.length * 50, y: prevNodes.length * 150 }, // Layout automático simples
          data: { 
            agentId: payload.agent_id, 
            content: payload.content, 
            toolCalled: payload.tool_called,
            isFinal: payload.is_final 
          },
        };

        // Cria a aresta (Edge) se houver um parent_id (pensamento sequencial)
        if (payload.parent_id) {
           setEdges((prevEdges) => addEdge({
               id: `e-${payload.parent_id}-${payload.id}`,
               source: payload.parent_id,
               target: payload.id,
               animated: !payload.is_final, // Anima a linha enquanto o agente "pensa"
           }, prevEdges));
        }

        return [...prevNodes, newNode];
      });
    });

    return () => {
      unlisten.then(f => f());
    };
  }, []);

  return { nodes, edges, onNodesChange, onEdgesChange, setNodes };
}
```

## 3. O Nó de Divulgação Progressiva (`ThoughtNode.tsx`)

Este é o componente visual mais importante do SODA. Para um perfil **2e (Dupla Excepcionalidade)**, ver um _wall of text_ com logs de sistema, raciocínio interno e chamadas de API drena a energia.

O `ThoughtNode` implementa o **Progressive Disclosure**:

1. **Estado Base:** Mostra apenas a intenção (ex: _"Consultando Memória..."_ ou _"Pensando..."_).
2. **Estado Expandido (On Click):** Revela o raciocínio bruto (ex: os parâmetros passados para o `Jcodemunch MCP`).

```tsx
// src/components/canvas/ThoughtNode.tsx
import { useState } from 'react';
import { Handle, Position } from '@xyflow/react';
import { BrainCircuit, Wrench, ChevronDown, ChevronUp } from 'lucide-react';

export function ThoughtNode({ data }: { data: any }) {
  const [isExpanded, setIsExpanded] = useState(false);

  // Define as cores baseadas no tipo de ação do agente
  const isTool = !!data.toolCalled;
  const borderColor = isTool ? 'border-orange-500' : 'border-blue-500';
  const bgColor = data.isFinal ? 'bg-zinc-900' : 'bg-zinc-800 animate-pulse';

  return (
    <div className={`w-80 rounded-xl border-2 ${borderColor} ${bgColor} text-gray-100 shadow-2xl font-sans overflow-hidden transition-all duration-300`}>
      {/* Ponto de conexão de entrada (Top) */}
      <Handle type="target" position={Position.Top} className="w-3 h-3 bg-gray-500" />

      {/* Header (Resumo Visual) */}
      <div 
        className="flex items-center justify-between p-3 cursor-pointer hover:bg-zinc-700/50"
        onClick={() => setIsExpanded(!isExpanded)}
      >
        <div className="flex items-center gap-2">
          {isTool ? <Wrench size={18} className="text-orange-400" /> : <BrainCircuit size={18} className="text-blue-400" />}
          <span className="font-bold text-sm tracking-wide">
            {isTool ? `MCP: ${data.toolCalled}` : data.agentId}
          </span>
        </div>
        <button className="text-gray-400 hover:text-white">
          {isExpanded ? <ChevronUp size={16} /> : <ChevronDown size={16} />}
        </button>
      </div>

      {/* Área de Divulgação Progressiva (Conteúdo Expandido) */}
      {isExpanded && (
        <div className="p-3 border-t border-zinc-700 bg-black/40 text-sm max-h-60 overflow-y-auto font-mono text-gray-300">
           {data.content}
        </div>
      )}

      {/* Estado "Fechado" mas mostrando progresso se não finalizou */}
      {!isExpanded && !data.isFinal && (
        <div className="p-3 border-t border-zinc-700 text-xs text-gray-400 italic">
          Processando tokens...
        </div>
      )}

      {/* Ponto de conexão de saída (Bottom) */}
      <Handle type="source" position={Position.Bottom} className="w-3 h-3 bg-gray-500" />
    </div>
  );
}
```

## 4. O Palco Principal (`SodaCanvas.tsx`)

A união do Hook de Estado do Tauri com o Visual do React Flow.

```tsx
// src/components/canvas/SodaCanvas.tsx
import { ReactFlow, Background, Controls, MiniMap } from '@xyflow/react';
import '@xyflow/react/dist/style.css';

import { useAgentState } from '../../hooks/useAgentState';
import { ThoughtNode } from './ThoughtNode';
import { CommandInput } from '../CommandInput'; // Seu input de texto/voz

// Registra os nós customizados do SODA
const nodeTypes = {
  progressiveThought: ThoughtNode,
};

export function SodaCanvas() {
  const { nodes, edges, onNodesChange, onEdgesChange } = useAgentState();

  return (
    <div className="w-screen h-screen bg-[#0f0f11] flex flex-col relative">
      
      {/* O Canvas Infinito */}
      <div className="flex-1">
        <ReactFlow
          nodes={nodes}
          edges={edges}
          onNodesChange={onNodesChange}
          onEdgesChange={onEdgesChange}
          nodeTypes={nodeTypes}
          fitView
          className="soda-theme"
          minZoom={0.1}
        >
          {/* Fundo escuro sutil com pontos para orientar o olhar */}
          <Background color="#333" gap={20} size={1} />
          {/* Controles no canto para navegação espacial */}
          <Controls className="bg-zinc-800 border-zinc-700 fill-white" />
          <MiniMap nodeColor="#444" maskColor="rgba(0,0,0,0.7)" className="bg-zinc-900" />
        </ReactFlow>
      </div>

      {/* A Barra de Comando Soberana (Fica flutuando sobre o Canvas) */}
      <div className="absolute bottom-8 left-1/2 transform -translate-x-1/2 w-[600px] z-50">
         {/* Este componente chama a função invoke('ask_agent', { prompt }) do Tauri 
            ensinada na Parte 2. 
         */}
         <CommandInput />
      </div>

    </div>
  );
}
```

### O Fluxo Real na sua Tela:

1. Você digita na barra flutuante: _"Verifique os erros do meu projeto no diretório X"_.
2. A barra não vira um histórico de chat.
3. No Canvas, surge um nó azul: **[ Phi-4-mini ]**. Fica pulsando.
4. Logo abaixo, surge um nó laranja conectado por uma linha animada: **[ MCP: Jcodemunch ]**.
5. Se você clicar no nó laranja (Divulgação Progressiva), verá o log do MCP lendo o diretório X em Go.
6. A linha animada retorna para o nó azul, que para de pulsar, e exibe a conclusão compilada.

O controle visual e cognitivo volta 100% para você.