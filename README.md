# mg-JSONRPC

Leverage [JSON-RPC](https://www.jsonrpc.org) to communicate between your [masterGo](https://mastergo.com/) plugin and your Master Go UI.

## Introduction

从仓库[figma-rpc](https://github.com/Lona/figma-jsonrpc) fork而来，添加了`mg-jsonrpc`的API，使得在masterGo插件和masterGo UI之间进行通信。

## Installation

```bash
npm install mg-jsonrpc
```

## Usage

- Define your API in a separate file (in `api.ts` for example):

  ```ts
  import { createPluginAPI, createUIAPI } from "mg-jsonrpc";

  // those methods will be executed in the Figma plugin,
  // regardless of where they are called from
  export const api = createPluginAPI({
    ping() {
      return "pong";
    },
    setToken(token: string) {
      return mg.clientStorage.setAsync("token", token);
    },
    getToken() {
      return mg.clientStorage.getAsync("token");
    }
  });

  // those methods will be executed in the MasterGo UI,
  // regardless of where they are called from
  export const uiApi = createUIAPI({
    selectionChanged(selection) {
      return "pong";
    }
  });
  ```

- Use the UI API in the plugin:

  ```ts
  import { uiApi } from "./api";

  // This shows the HTML page in "ui.html".
  mg.showUI(__html__);

  mg.on("selectionchange", () => {
    uiApi.selectionChange(figma.currentPage.selection);
  });
  ```

- Use the API in the UI:

  ```ts
  import { api } from "./api";

  const pong = await api.ping();
  const token = await api.getToken();
  await api.setToken("new token");
  ```

The typescript definition of the API is automatically inferred from the methods passed to `rpc` :sparkles:.

> :warning: You always need to import the API in both the plugin and the UI, even if you aren't using it. It is necessary so that both part can handle calls from each other.


## License

MIT
