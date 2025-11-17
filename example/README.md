# Vite + React + TypeScript Example

This example demonstrates how to use the Deploy Walrus Site action with a site that requires a build step.

## What This Example Shows

- Setting up a Vite + React + TypeScript project
- Building the project before deployment
- Using the Deploy Walrus Site action in a GitHub workflow
- Automatic PR creation when deployment resources change

## Local Development

```bash
npm install
npm run dev
```

Visit `http://localhost:5173` to see the site locally.

## Building

```bash
npm run build
```

The built site will be in the `dist/` directory.

## Deploying with GitHub Actions

This example includes a workflow that:

1. Checks out the repository
2. Sets up Node.js
3. Installs dependencies
4. Builds the site
5. Deploys to Walrus using the action

### Setting Up Secrets

Before deploying, add your Sui credentials to GitHub Secrets:

1. Go to your repository **Settings** → **Secrets and variables** → **Actions**
2. Add your authentication credentials (choose one method):
   - **Method 1**: `SUI_ADDRESS` (as a variable) and `SUI_KEYSTORE` (as a secret)
   - **Method 2**: `SUI_BECH32_PRIVATE_KEY` (as a secret)
   - **Method 3**: `SUI_SECRET_PHRASE` (as a secret)

See the main [README](../deploy/README.md) for detailed security best practices.

### Example Workflow

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy Vite React App to Walrus
on:
  push:
    branches: [main]
  workflow_dispatch:
    inputs:
      SUI_NETWORK:
        description: 'Sui network to deploy to'
        required: false
        default: 'mainnet'
      EPOCHS:
        description: 'Epochs to keep the site alive'
        required: false
        default: '5'

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
        working-directory: example

      - name: Build application
        run: npm run build
        working-directory: example

      - name: Deploy to Walrus
        uses: MystenLabs/deploy-walrus-site-action@v1
        with:
          SUI_NETWORK: ${{ inputs.SUI_NETWORK || 'mainnet' }}
          SUI_ADDRESS: ${{ vars.SUI_ADDRESS }}
          SUI_KEYSTORE: ${{ secrets.SUI_KEYSTORE }}
          DIST: example/dist
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          EPOCHS: ${{ inputs.EPOCHS || '5' }}
```

## Project Structure

```
example/
├── src/              # React source code
├── public/           # Static assets
├── dist/             # Built output (created by npm run build)
├── index.html        # HTML template
├── package.json      # Dependencies and scripts
├── tsconfig.json     # TypeScript configuration
└── vite.config.ts    # Vite configuration
```

## Technologies Used

- **React 19**: UI framework
- **TypeScript**: Type-safe JavaScript
- **Vite**: Fast build tool and dev server
- **ESLint**: Code linting

## Customization

This is a minimal example. You can extend it with:
- Additional React components
- Routing (React Router)
- State management (Redux, Zustand, etc.)
- API calls
- Custom styling (Tailwind, styled-components, etc.)

## Learn More

- [Vite Documentation](https://vitejs.dev/)
- [React Documentation](https://react.dev/)
- [TypeScript Documentation](https://www.typescriptlang.org/)
- [Deploy Walrus Site Action](../deploy/README.md)
