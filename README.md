# Walrus Sites GitHub Actions

A collection of GitHub Actions for deploying and managing static sites on Walrus.

## Available Actions

### [Deploy Walrus Site](./deploy/README.md)

Deploy static sites to Walrus and publish them as webpages.

**Quick Example:**
```yaml
- uses: MystenLabs/walrus-sites-github-actions/deploy@v1
  with:
    SUI_ADDRESS: ${{ vars.SUI_ADDRESS }}
    SUI_KEYSTORE: ${{ secrets.SUI_KEYSTORE }}
    DIST: ./dist
    SITE_BUILDER_VERSION: 'v2'
```

**Features:**
- Multiple authentication methods (Address + Keystore, Bech32 Private Key, Secret Recovery Phrase)
- Automatic PR creation when deployment resources change
- Support for site-builder v1 and v2
- Configurable storage epochs
- Network selection (mainnet/testnet)

**Documentation:** [deploy/README.md](./deploy/README.md)

## Getting Started

1. **Choose an Action** - Start with the [Deploy Walrus Site](./deploy/README.md) action for deploying static sites

2. **Set Up Authentication** - Configure your Sui credentials as GitHub Secrets:
   - Go to **Settings** → **Secrets and variables** → **Actions**
   - Add your credentials (see individual action documentation for details)

3. **Add to Workflow** - Include the action in your `.github/workflows/*.yml` file

## Example Project

See the [example/](./example/) directory for a complete Vite + React + TypeScript project that demonstrates:
- Building a site with npm
- Deploying to Walrus using the Deploy action
- Automatic PR creation on resource updates

## Security

**NEVER** hardcode private keys, keystores, or secret phrases in your workflow files. Always use [GitHub encrypted secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets).

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

See [LICENSE](./LICENSE) file for details.
