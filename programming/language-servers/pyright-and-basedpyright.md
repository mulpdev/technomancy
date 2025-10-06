# Pyright

See also Ghidra for ghidra stubs in pyright

```sh
npm install --global pyright
```

# basedpyright

```sh
pip install basedpyright
```

https://github.com/LazyVim/LazyVim/discussions/3350#discussioncomment-9584985
```lua
  {
    "neovim/nvim-lspconfig",
    opts = {
      servers = {
        basedpyright = {
          settings = {
            basedpyright = {
			  analysis = {
              typeCheckingMode = "standard",
 			  },
            },
          },
        },
      },
    },
  },
```


# pyrightconfig.json
put in {PROJECT_ROOT}, and it is very strict about json formatting

minimal
```json
{
  "exclude": [
    "**/__pycache__"
  ]
}
```

set the `report{ErrorName}` to
- `true` `false` (no quotes, it's a boolean)
- `"none"`, `"hint"`, `"warning"`, `"information"`, or `"error"`


Example from https://docs.basedpyright.com/dev/configuration/config-files/
```json
{
  "include": [
    "src"
  ],

  "exclude": [
    "**/node_modules",
    "**/__pycache__",
    "src/experimental",
    "src/typestubs"
  ],

  "ignore": [
    "src/oldstuff"
  ],

  "defineConstant": {
    "DEBUG": true
  },

  "stubPath": "src/stubs",

  "reportMissingImports": "error",
  "reportMissingTypeStubs": false,

  "pythonVersion": "3.6",
  "pythonPlatform": "Linux",

  "executionEnvironments": [
    {
      "root": "src/web",
      "pythonVersion": "3.5",
      "pythonPlatform": "Windows",
      "extraPaths": [
        "src/service_libs"
      ],
      "reportMissingImports": "warning"
    },
    {
      "root": "src/sdk",
      "pythonVersion": "3.0",
      "extraPaths": [
        "src/backend"
      ]
    },
    {
      "root": "src/tests",
      "reportPrivateUsage": false,
      "extraPaths": [
        "src/tests/e2e",
        "src/sdk"
      ]
    },
    {
      "root": "src"
    }
  ]
}
```

# Comments to disable 

```python
# type:ignore
# pyright:ignore


# pyright:ignore[reportAny] # <- required by reportIgnoreCommentWithoutRule
```