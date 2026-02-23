# Dune Transaction History

Fetch transaction history for wallets.

## Configuration

- **Blockchain**: arbitrum
- **Limit**: 100

## Usage

```tsx
import { useTransactionHistory } from '@/hooks/useTransactionHistory';

function TxHistory({ address }) {
  const { data: transactions } = useTransactionHistory({
    address,
    limit: 100
  });
  
  return <TransactionHistory transactions={transactions} />;
}
```
