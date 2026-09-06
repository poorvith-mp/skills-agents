# n8n Custom Nodes Reference

Developing custom TypeScript nodes for the n8n ecosystem.

## Declarative vs Programmatic Nodes
- **Declarative**: Define properties and routing in JSON-like TypeScript descriptions for simple REST integrations.
- **Programmatic**: Implement the `execute()` method directly when complex hashing, custom binary data, or multi-step logic is required:
```typescript
import { IExecuteFunctions, INodeExecutionData, INodeType, INodeTypeDescription } from 'n8n-workflow';

export class CustomTool implements INodeType {
  description: INodeTypeDescription = {
    displayName: 'Custom Tool',
    name: 'customTool',
    group: ['transform'],
    version: 1,
    description: 'Custom processing node',
    defaults: { name: 'Custom Tool' },
    inputs: ['main'],
    outputs: ['main'],
    properties: []
  };

  async execute(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
    const items = this.getInputData();
    return [items];
  }
}
```
