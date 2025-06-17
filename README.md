## Plano de Desenvolvimento: Biblioteca Angular de Visualização 3D de Hiper-Grafos + Sample App Completo

A seguir, um roteiro em **12 etapas** para criar:

1. Uma **biblioteca Angular reutilizável** (`ngx-hypergraph3d`) que expõe componentes e serviços para definição, renderização e interação com hiper-grafos 3D.
2. Um **sample-app** exaustivo (`hypergraph-demo`) que demonstra **todas** as funcionalidades configuráveis da biblioteca, com testes e documentação integrados.

Cada passo inclui **requisitos**, **resultados mensuráveis** e uma **saída útil** (ex.: artefato, teste, documentação).

---

## 1. Setup do Monorepo e Ferramentas

* **Requisitos**

  * Inicializar um workspace com [Nx](https://nx.dev) ou \[Angular CLI + ng-workspace] para múltiplos projetos.
  * Instalar dependências básicas: Angular v15+, TypeScript, Jest/Karma, Cypress.
* **Resultado Mensurável**

  * `nx dep-graph` mostra apps `ngx-hypergraph3d` e `hypergraph-demo`.
  * CI Básico executa `npm test` (unit) e `npm run lint` sem erros.
* **Documentação**

  * README raiz descrevendo estrutura, comandos e convenções de commits.
  * ADR inicial justificando escolha de monorepo e ferramentas.

---

## 2. Esqueleto da Biblioteca `ngx-hypergraph3d`

* **Requisitos**

  * Gerar biblioteca com `nx g @nrwl/angular:library ngx-hypergraph3d --publishable`.
  * Estrutura de pastas:

    ```
    libs/ngx-hypergraph3d/
      src/lib/
        components/
        services/
        models/
        utils/
      README.md
    ```
* **Resultado Mensurável**

  * `npm pack dist/libs/ngx-hypergraph3d` gera o artefato publicável.
  * `docs/api` gerado com TypeDoc cobrindo todos os módulos públicos.
* **Documentação**

  * README da lib com **Getting Started**, **API Overview**, **Contributing**.

---

## 3. Modelo de Dados e Configuração

* **Requisitos**

  * Definir interfaces TypeScript:

    ```ts
    interface Node3D { id: string; position: Vector3; color?: string; opacity?: number; }
    interface Hyperedge3D { id: string; nodes: string[]; color?: string; shader?: string; }
    interface HypergraphConfig { 
      nodes: Node3D[]; 
      hyperedges: Hyperedge3D[]; 
      defaultNodeColor: string; 
      defaultEdgeColor: string; 
      enableHistory: boolean;
    }
    ```
  * Serviço de **validação** de config e geração de IDs.
* **Resultado Mensurável**

  * **10** testes unitários (Jest) cobrindo validação e geração de IDs.
  * Cobertura de 100% em `libs/ngx-hypergraph3d/src/lib/models`.
* **Documentação**

  * Exemplo de JSON de configuração no README e no site de docs.

---

## 4. Componente 3D Base com Three.js

* **Requisitos**

  * Componente `<ngx-hypergraph3d-viewer [config]="..." />` que:

    * Renderiza esferas (nós) e convex hull ou linhas (hiper-arestas) em 3D.
    * Usa `OrbitControls` para ângulo, posição e zoom da câmera.
    * Aceita `theme: 'dark'|'light'`.
* **Resultado Mensurável**

  * Demo inicial: nó único com hiper-aresta de 2 nós.
  * Teste end-to-end (Cypress) interagindo com zoom e orbit.
* **Documentação**

  * Guia de propriedades do componente (`@Input()`) e `@Output()` de eventos (`clickNode`, `hoverEdge`).

---

## 5. Históricos de Alterações e Timeline

* **Requisitos**

  * Serviço `HypergraphHistoryService` registrando cada mutação (add/remove nodes/edges, color change).
  * API para **undo/redo** em níveis ilimitados e branching (árvore de estados).
* **Resultado Mensurável**

  * API coberta com 15 testes unitários (incluindo branch undo).
  * Método `getTimeline(): TimelineNode[]` retornando estrutura de estados.
* **Documentação**

  * Diagrama UML do serviço histórico e exemplos de uso no README.

---

## 6. Sample-App: Editor de Hiper-Grafos

* **Requisitos**

  * Aplicação Angular (`apps/hypergraph-demo`) que importa `ngx-hypergraph3d`.
  * UI:

    * **Toolbar**: botões para adicionar/remover nós e arestas, alterar cores, aplicar shaders.
    * **Timeline Panel**: visualização dos estados, permissão de branching e voltar a qualquer ponto.
    * **Inspector**: propriedades do nó/edge selecionado (cor, opacidade, borda).
  * Componentes Angular com Reactive Forms para edição.
* **Resultado Mensurável**

  * Demonstração:

    1. Adicionar nó → esfera aparece.
    2. Conectar nós → linha ou convex aparece em 3D.
    3. Alterar cor ou shader do nó/edge em tempo real.
    4. Navegar na timeline e branchar.
  * Cypress E2E cobrindo todos 4 casos acima.
* **Documentação**

  * Guia “Getting Started” específico do demo, com screenshots e vídeo curto (GIF).

---

## 7. Layout 2D com Sprites e Overlays

* **Requisitos**

  * Implementar “billboard sprites” para labels e tooltips:

    * Sempre virados à câmera, não afetados pela luz.
    * Posição 2D coerente com a projeção 3D.
  * APIs para customizar layout (grid, radial, force-directed em 2D).
* **Resultado Mensurável**

  * Exemplo no demo: alternar entre layout 3D puro e 2D overlay.
  * Unit-test validando cálculo de posição da sprite.
* **Documentação**

  * Seção no README da lib mostrando os diferentes layouts e opções de customização.

---

## 8. Customização de Pontas de Arestas

* **Requisitos**

  * Suporte a múltiplos tipos de pontas: seta, círculo, losango, diamante.
  * Parâmetros: `edgeHeadType`, `edgeHeadSize`, `edgeHeadColor`.
* **Resultado Mensurável**

  * Demo com dropdown para selecionar tipo de ponta e atualizar em tempo real.
  * Teste unitário assegurando que `edgeHeadType` gera geometria correta.
* **Documentação**

  * Tabela de tipos suportados, parâmetros e exemplos visuais.

---

## 9. Custom Shaders e Material Options

* **Requisitos**

  * Permitir configuração de shaders GLSL customizados para nós e arestas.
  * API: `nodeShader`, `edgeShader` como strings GLSL ou referências a arquivos.
* **Resultado Mensurável**

  * Demo carregando shader de glow e shader de transparência gradiente.
  * Teste E2E assegurando aplicação do shader.
* **Documentação**

  * Guia de como escrever e anexar shaders, com trecho mínimo de exemplo GLSL.

---

## 10. Eventos e Animações de Transformação

* **Requisitos**

  * Expor eventos Angular (`@Output()`) para `nodeClick`, `edgeClick`, `historyChange`.
  * Função para aplicar **regras de transformação genéricas**:

    ```ts
    applyRule(pattern: GraphPattern, transform: (match) => Action[]): void;
    ```
  * Animações contínuas: usar `tween.js` ou Three.js `AnimationMixer`.
* **Resultado Mensurável**

  * Demo: aplicar regra “duplicar nó” gera animação suave de expansão.
  * Teste unitário mockando `applyRule` e assegurando chamada de callback.
* **Documentação**

  * Exemplos de regras, API de transformação e integração com animações.

---

## 11. Configuração Avançada

* **Requisitos**

  * Arquivo JSON/YAML de configuração para cores, shaders, estilos, histórico.
  * `forRoot(config)` para module Angular, e `forChild()` para múltiplas instâncias.
* **Resultado Mensurável**

  * Sample-app inicializa duas visualizações independentes com configs distintas.
  * Teste unitário no módulo Angular para `forRoot` e `forChild`.
* **Documentação**

  * Exemplo completo de `app.module.ts` mostrando importações e configurações.

---

## 12. Entrega, Versionamento e Publicação

* **Requisitos**

  * Definir **semantic-release** para automatizar versões e changelog.
  * Publicar no npm com `@your-org/ngx-hypergraph3d`.
  * Configurar GitHub Actions para build, test, lint e release quando criado tag `v*`.
* **Resultado Mensurável**

  * Versão `1.0.0` publicada no npm, badge de build e coverage no README.
* **Documentação**

  * Guia de Release: descreve convenções de commit (Conventional Commits) e uso da CLI de release.

---

### Outras Features Sugeridas

* **Exportação/Importação** de grafos em formatos JSON, GraphML e GEXF.
* **Colaboração em tempo real** usando WebSocket/CRDT para edição simultânea.
* **Integração com VR/AR** via WebXR para imersão nos hiper-grafos.
* **Análise de métricas** (centralidade, clusters) expostas como serviços Angular.
* **Plugins**: arquitetura para adicionar novos tipos de nós e arestas sem alterar core.

---

Este plano garante **documentação obrigatória** em cada passo e **resultados tangíveis** que podem ser validados por testes automatizados, demos e artefatos de build.
