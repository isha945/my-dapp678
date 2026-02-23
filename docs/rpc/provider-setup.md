# RPC Provider Setup

# RPC Provider Configuration

Multi-provider RPC setup with automatic failover for Arbitrum.

## Providers

- **Primary**: alchemy
- **Fallbacks**: public

## Features

- **WebSocket Support**: Enabled
- **Health Checks**: Every 30000ms
- **Retry Attempts**: 3
- **Privacy Mode**: Disabled

## Setup

### 1. Get API Keys


#### Alchemy
1. Go to [Alchemy Dashboard](https://dashboard.alchemy.com)
2. Create a new app for Arbitrum
3. Copy your API key






### 2. Configure Environment Variables

```bash
# .env.local
NEXT_PUBLIC_ALCHEMY_API_KEY=your_alchemy_key



```

## Usage

### Get a Public Client

```typescript
import { getPublicClient } from '@/lib/rpc/client-factory';

const client = getPublicClient();
const blockNumber = await client.getBlockNumber();
```

### Use the Provider Hook

```typescript
import { useProvider } from '@/hooks/useProvider';

function MyComponent() {
  const { client, isHealthy, currentProvider } = useProvider();
  
  // client is automatically the healthiest available provider
}
```

### Manual Health Check

```typescript
import { checkProviderHealth } from '@/lib/rpc/health-check';

const health = await checkProviderHealth();
console.log(health);
// { alchemy: { healthy: true, latency: 45 }, ... }
```

## Provider Comparison

| Provider | Free Tier | Rate Limit | WebSocket | Archive |
|----------|-----------|------------|-----------|---------|
| Alchemy | 300M CU/month | High | Yes | Yes |
| QuickNode | Limited | Medium | Yes | Paid |
| Infura | 100K/day | Medium | Yes | Paid |
| Ankr | Unlimited | Low | Yes | Yes |
| 1RPC | Unlimited | Low | No | No |
| Public | Unlimited | Very Low | No | No |

## Privacy Mode

When privacy mode is enabled, requests are routed through 1RPC which:
- Does not log IP addresses
- Does not track requests
- Provides MEV protection
