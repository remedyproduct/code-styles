Basic linter, formatter & editor config for Typescript

## Required Dependencies

In the project directory, you can run:

```shell
  $ yarn add --dev typescript, eslint, @types/body-parser, @typescript-eslint/eslint-plugin, @typescript-eslint/parser, eslint-config-airbnb-typescript, eslint-config-prettier, eslint-plugin-import, eslint-plugin-jsx-a11y, eslint-plugin-prettier, eslint-plugin-react, eslint-plugin-react-hooks
```

or

```shell
  $ npm install --save-dev typescript, eslint, @types/body-parser, @typescript-eslint/eslint-plugin, @typescript-eslint/parser, eslint-config-airbnb-typescript, eslint-config-prettier, eslint-plugin-import, eslint-plugin-jsx-a11y, eslint-plugin-prettier, eslint-plugin-react, eslint-plugin-react-hooks
```

## Look for the scripts area in package.json and add this two lines (optional)

```
  "lint": "eslint -c .eslintrc --ext .ts src",
  "format": "prettier --write src/**/*.{ts,json}",
```

### Basic Features

1. Auto Eslint formatting on Save.

#### VS Code users

Copy `.vscode` folder into your project root folder.
