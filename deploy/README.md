# Deploy Walrus Site GitHub Action

A GitHub Action for deploying static sites to Walrus and publishing them as webpages.

## Security Best Practices

### Handling Private Keys and Secrets

This action requires Sui account credentials to deploy sites. **NEVER** hardcode private keys, keystores, or secret phrases directly in your workflow files or commit them to your repository.

#### Common Security Pitfalls to Avoid

- **Don't** paste private keys directly into workflow YAML files
- **Don't** commit `.env` files containing secrets
- **Don't** log or echo secret values for debugging
- **Don't** use secrets in pull requests from forks (they won't work anyway)
- **Don't** share the same deployment key across multiple projects without proper isolation

#### Correct Usage: GitHub Secrets

Always use [GitHub encrypted secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) to store sensitive information:

1. Navigate to your repository **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Add your secrets with appropriate names (e.g., `SUI_KEYSTORE`, `SUI_BECH32_PRIVATE_KEY`)
4. Reference them in workflows using `${{ secrets.SECRET_NAME }}`

For non-sensitive values like `SUI_ADDRESS`, you can use [GitHub variables](https://docs.github.com/en/actions/learn-github-actions/variables) instead of secrets for better visibility.

## Usage

This action operates on existing static files. You typically need to:
1. Check out your repository
2. Build your site (if needed)
3. Deploy with this action

### Three Authentication Methods

Choose **ONE** of the following methods to authenticate:

#### Method 1: Address + Keystore (Recommended for CI/CD)

```yaml
- uses: MystenLabs/deploy-walrus-site-action@v1
  with:
    SUI_ADDRESS: ${{ vars.SUI_ADDRESS }}
    SUI_KEYSTORE: ${{ secrets.SUI_KEYSTORE }}
    DIST: ./dist
```

#### Method 2: Bech32 Private Key

```yaml
- uses: MystenLabs/deploy-walrus-site-action@v1
  with:
    SUI_BECH32_PRIVATE_KEY: ${{ secrets.SUI_BECH32_PRIVATE_KEY }}
    DIST: ./dist
```

#### Method 3: Secret Recovery Phrase

```yaml
- uses: MystenLabs/deploy-walrus-site-action@v1
  with:
    SUI_SECRET_PHRASE: ${{ secrets.SUI_SECRET_PHRASE }}
    DIST: ./dist
```

## Complete Example

See the `example/` directory for a complete Vite + React + TypeScript example that demonstrates:
- Building a site with a build step
- Deploying to Walrus using this action
- Creating PRs when resources are updated

Example workflow:

```yaml
name: Deploy to Walrus
on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 'latest'

      - name: Install dependencies
        run: npm install

      - name: Build site
        run: npm run build

      - name: Deploy to Walrus
        uses: MystenLabs/deploy-walrus-site-action@v1
        with:
          SUI_ADDRESS: ${{ vars.SUI_ADDRESS }}
          SUI_KEYSTORE: ${{ secrets.SUI_KEYSTORE }}
          DIST: ./dist
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          EPOCHS: '5'
```

## Inputs

### Required Inputs

| Input | Description |
|-------|-------------|
| `DIST` | Path to the directory with the built site |

### Authentication Inputs (Choose ONE method)

| Input | Description | Example |
|-------|-------------|---------|
| `SUI_ADDRESS` | Sui address (requires `SUI_KEYSTORE`) | `0x1234...` |
| `SUI_KEYSTORE` | Sui keystore file content (requires `SUI_ADDRESS`) | JSON array |
| `SUI_BECH32_PRIVATE_KEY` | Bech32 encoded private key | `suiprivkey1...` |
| `SUI_SECRET_PHRASE` | Mnemonic recovery phrase | `word1 word2 ...` |

### Optional Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `SUI_NETWORK` | Sui network to use | `mainnet` |
| `WALRUS_CONFIG` | Custom Walrus configuration file content | Downloads from Walrus repo |
| `SITES_CONFIG` | Custom sites-config.yaml content | Downloads from walrus-sites repo |
| `WS_RESOURCES` | Path to ws-resources.json file | `$DIST/ws-resources.json` |
| `EPOCHS` | Number of epochs to keep the site | `5` |
| `CHECK_EXTEND` | Whether to check and extend storage period | `false` |
| `GITHUB_TOKEN` | Token for creating PRs when resources change | (none) |

## Security Features

This action includes several security measures:

- **Input Validation**: Ensures exactly one authentication method is provided
- **Sui Binary Download**: Uses official Sui tooling for key derivation
- **No Secret Logging**: Sensitive values are not echoed or logged
- **Ed25519 Scheme**: Uses Ed25519 cryptographic scheme for key operations

## Automatic PR Creation

When you provide `GITHUB_TOKEN`, the action will automatically create a pull request if the `ws-resources.json` file changes during deployment. This helps track resource changes over time.

Required permissions:
```yaml
permissions:
  contents: write
  pull-requests: write
```

## Troubleshooting

### Authentication Errors

**Error**: "Must provide one of the following Sui account setup methods"
- **Solution**: Ensure you're providing at least one authentication method

**Error**: "Only one Sui account setup method can be used at a time"
- **Solution**: Remove extra authentication inputs, use only one method

**Error**: "Both SUI_ADDRESS and SUI_KEYSTORE must be provided together"
- **Solution**: If using Method 1, provide both values

### Deployment Errors

**Error**: "Insufficient funds" or similar Sui errors
- **Solution**: Ensure your Sui address has sufficient tokens for deployment

**Error**: Cannot find distribution directory
- **Solution**: Verify the `DIST` path is correct and the build step succeeded

## Additional Resources

- [Walrus Documentation](https://docs.walrus.site/)
- [Sui Documentation](https://docs.sui.io/)
- [GitHub Actions Security Best Practices](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)
- [Managing Encrypted Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)

## License

This action is provided as-is for deploying sites to Walrus. Check the main Walrus Sites repository for licensing information.
