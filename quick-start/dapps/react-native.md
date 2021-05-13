---
description: Quick Start For Dapps using React-Native
---

# React-Native

## Quick Start For Dapps \(React-Native\)

A drop-in library which helps easily connect your [**React Native**](https://reactnative.dev) dapps to [**Ethereum**](https://ethereum.org) Wallets on [**Android**](https://reactnative.dev), [**iOS**](https://reactnative.dev) and the [**Web**](https://github.com/necolas/react-native-web).

> **Notice:** This library assumes you have already enabled prerequisite support for [**Web3**](https://github.com/ChainSafe/web3.js) inside your application. This can be done by creating a new project using [`npx create-react-native-dapp`](https://github.com/cawfree/create-react-native-dapp), or by introducing support for Web3 in an existing project by using [`npx rn-nodeify --install --hack`](https://github.com/tradle/rn-nodeify).

For more details, check out the [documentation](https://docs.walletconnect.org).

## Installing

To get started, install `@walletconnect/react-native-dapp`:

```bash
yarn add @walletconnect/react-native-dapp
```

If you haven't already, you may also need to install [`react-native-svg`](https://github.com/react-native-svg/react-native-svg) alongside a persistent storage provider such as [`@react-native-async-storage/async-storage`](https://github.com/react-native-async-storage/async-storage):

```bash
yarn add react-native-svg @react-native-async-storage/async-storage
```

## Architecture

This library is implemented using the [**React Context API**](https://reactjs.org/docs/context.html), which is used to help make an instance of a [`connector`](https://docs.walletconnect.org/client-api) accessible globally throughout your application. This permits you to use a uniform instance within even deeply nested components, and ensures your rendered application is always synchronized against the connector state.

### `ConnectWalletsProvider`

At the root of your application, you can declare a [`ConnectWalletsProvider`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/providers/ConnectWalletsProvider.tsx) which controls access and persistence to a [**connector**](https://docs.walletconnect.org/client-api) instance:

```typescript
import * as React from 'react';
import ConnectWalletsProvider from '@walletconnect/react-native-dapp';
import AsyncStorage from '@react-native-async-storage/async-storage';

export default function App(): JSX.Element {
  return (
    <ConnectWalletsProvider
      redirectUrl={Platform.OS === 'web' ? window.location.origin : 'yourappscheme://'}
      storageOptions= {{
        asyncStorage AsyncStorage,
      }}>
      <>{/* awesome app here */}</>
    </ConnectWalletsProvider>
  );
}
```

Above, we pass the [`ConnectWalletsProvider`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/providers/ConnectWalletsProvider.tsx) two required parameters; `redirectUrl` and `storageOptions`:

* The `redirectUrl` is used to help control navigation between external wallets and your application. On the `web`, you only need to specify a valid application route; whereas on mobile platforms, you must [**specify a deep link URI scheme**](https://docs.expo.io/workflow/linking/#universaldeep-links-without-a-custom-scheme).
* The `storageOptions` prop allows you to specify the storage engine which must be used to persist session data.
  * Although in our examples we use [`@react-native-async-storage/async-storage`](https://github.com/react-native-async-storage/async-storage), this can be which engine you please, provided it conforms to the [`IAsyncStorage`](https://github.com/pedrouid/keyvaluestorage) generic storage interface declaration.

Notably, the [`ConnectWalletsProvider`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/providers/ConnectWalletsProvider.tsx) optionally accepts `ConnectWallets` configuration arguments as defined by the [`IConnectWalletsOptions`](https://github.com/ConnectWallets/walletconnect-monorepo/tree/next/packages/helpers/utils) interface:

```typescript
import * as React from 'react';
import ConnectWalletsProvider from '@walletconnect/react-native-dapp';
import AsyncStorage from '@react-native-async-storage/async-storage';

export default function App(): JSX.Element {
  return (
    <ConnectWalletsProvider
      bridge="https://bridge.walletconnect.org"
      clientMeta={{
        description: 'Connect with ConnectWallets',
        url: 'https://walletconnect.org',
        icons: ['https://walletconnect.org/walletconnect-logo.png'],
        name: 'ConnectWallets',
      }}
      redirectUrl={Platform.OS === 'web' ? window.location.origin : 'yourappscheme://'}
      storageOptions= {{
        asyncStorage AsyncStorage,
      }}>
      <>{/* awesome app here */}</>
    </ConnectWalletsProvider>
  );
}
```

In the snippet above, aside from the required props, we can see the default configuration of the [`ConnectWalletsProvider`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/providers/ConnectWalletsProvider.tsx).

> **Tip:** Your custom options are merged _deeply_ against this default configuration. Therefore it's possible to override individual nested properties without being required to define _all_ of them.

#### `withConnectWallets`

Alternatively to manually using the [`ConnectWalletsProvider`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/providers/ConnectWalletsProvider.tsx), you can use the [`withConnectWallets`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/hooks/useConnectWallets.ts) higher order component which will wrap your root application in a [`ConnectWalletsProvider`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/providers/ConnectWalletsProvider.tsx) for you:

```typescript
import * as React from "react";
import {
  withConnectWallets,
  useConnectWallets,
} from "@walletconnect/react-native-dapp";
import AsyncStorage from "@react-native-async-storage/async-storage";

function App(): JSX.Element {
  const connector = useConnectWallets(); // valid
  return <>{/* awesome app here */}</>;
}

export default withConnectWallets(App, {
  clientMeta: {
    description: "Connect with ConnectWallets",
  },
  redirectUrl:
    Platform.OS === "web" ? window.location.origin : "yourappscheme://",
  storageOptions: {
    asyncStorage: AsyncStorage,
  },
});
```

This is almost identical in functionality to the manual implementation of a [`ConnectWalletsProvider`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/providers/ConnectWalletsProvider.tsx), with the key difference that we're able to make a call to [`useConnectWallets`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/hooks/useConnectWallets.ts) directly from the `App` component. By contrast, in the previous example only child components of the [`ConnectWalletsProvider`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/providers/ConnectWalletsProvider.tsx) may be able to invoke this hook.

### `useConnectWallets`

The [`useConnectWallets`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/hooks/useConnectWallets.ts) hook provides access to a [`ConnectWallets`](https://docs.walletconnect.org/client-api) `connector` instance which is accessible on Android, iOS and the Web. This conforms to the original specification:

```typescript
import AsyncStorage from '@react-native-async-storage/async-storage';
import { useConnectWallets, withConnectWallets } from '@walletconnect/react-native-dapp';
import * as React from 'react';

function App(): JSX.Element {
  const connector = useConnectWallets();
  if (!connector.connected) {
    /**
     *  Connect! 🎉
     */
    return <Button title="Connect" onPress={() => connector.connect())} />;
  }
  return <Button title="Kill Session" onPress={() => connector.killSession()} />;
}

export default withConnectWallets(App, {
  redirectUrl: Platform.OS === 'web' ? window.location.origin : 'yourappscheme://',
  storageOptions: {
    asyncStorage AsyncStorage,
  },
});
```

## Customization

`@walletconnect/react-native-dapp` also permits you to customize the presentation of the [`QrcodeModal`](https://github.com/ConnectWallets/walletconnect-docs/tree/074ef6d866a4790726bc2159d80cdc4f35a969ea/quick-start/dapps/src/components/QrcodeModal.tsx). This is achieved by passing the [**Render Callback**](react-native.md) prop, `renderQrcodeModal`, to our calls to `withConnectWallets` or instances of `ConnectWalletsProvider`.

For example, you could choose to render a wallet selection using a `BottomSheet` opposed to a `Modal`:

```typescript
import AsyncStorage from '@react-native-async-storage/async-storage';
import BottomSheet from 'react-native-reanimated-bottom-sheet';
import { Image, Text, TouchableOpacity } from 'react-native';
import {
  useConnectWallets,
  withConnectWallets,
  RenderQrcodeModalProps,
  WalletService,
} from '@walletconnect/react-native-dapp';
import * as React from 'react';

function CustomBottomSheet({
  walletServices,
  visible,
  connectToWalletService,
  uri,
}: RenderQrcodeModalProps): JSX.Element {
  const renderContent = React.useCallback(() => {
    return walletServices.map((walletService: WalletService, i: number) => (
      <TouchableOpacity key={`i${i}`} onPress={() => connectToWalletService(walletService, uri)}>
        <Image source={{ uri: walletService.logo }} />
        <Text>{walletService.name}</Text>
      </TouchableOpacity>
    ));
  }, [walletServices, uri]);
  return <BottomSheet renderContent={renderContent} {...etc} />;
};

function App(): JSX.Element {
  const connector = useConnectWallets();
  return <>{/* awesome custom app here */}</>;
}

export default withConnectWallets(App, {
  redirectUrl: Platform.OS === 'web' ? window.location.origin : 'yourappscheme://',
  storageOptions: {
    asyncStorage AsyncStorage,
  },
  renderQrcodeModal: (props: RenderQrcodeModalProps): JSX.Element => (
    <CustomBottomSheet {...props} />
  ),
});
```

