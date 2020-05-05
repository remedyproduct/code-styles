Basic linter, formatter & editor config for Typescript React apps.

## Pre-requisite

```shell
  $ npx create-react-app /*your-project-name*/ --template typescript
```

## Required Dependencies

In the project directory, you can run:

```shell
  $ yarn add -D eslint eslint-config-airbnb eslint-config-airbnb-typescript eslint-config-prettier eslint-config-react-app eslint-import-resolver-typescript eslint-loader eslint-plugin-flowtype eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react eslint-plugin-react-hooks babel-eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin prettier prettier-eslint prettier-eslint-cli eslint-plugin-prettier
```

or

```shell
  $ npm i -D --save-exact eslint eslint-config-airbnb eslint-config-airbnb-typescript eslint-config-prettier eslint-config-react-app eslint-import-resolver-typescript eslint-loader eslint-plugin-flowtype eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react eslint-plugin-react-hooks babel-eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin prettier prettier-eslint prettier-eslint-cli eslint-plugin-prettier
```

## Look for the scripts area in package.json and add this two lines (optional)

```
  "lint": "eslint --ext .js,.jsx,.ts,.tsx src --color",
  "format": "prettier --write src/**/*.{ts,tsx,scss,css,json}",
```

### Basic Features

1. Auto Eslint formatting on Save.
2. Ability to use absolute import paths without ejecting & manual webpack settings.

#### VS Code users

Copy `vscode` folder into your project root folder and rename it to `.vscode`
