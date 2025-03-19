# Smart Contract Upgrades with UUPS Pattern

This repository demonstrates how to implement upgradeable smart contracts using the Universal Upgradeable Proxy Standard (UUPS) pattern with Foundry. The project showcases a complete workflow for deploying, upgrading, and testing upgradeable contracts.

## Project Overview

The project implements a simple `Box` contract with two versions:
- **BoxV1**: Basic implementation with a storage variable and getter function
- **BoxV2**: Upgraded implementation that adds a setter function

The upgrade mechanism uses OpenZeppelin's UUPS (Universal Upgradeable Proxy Standard) pattern, which places the upgrade logic in the implementation contract rather than the proxy.

## Key Components

### Smart Contracts

- **BoxV1.sol**: Initial implementation with basic functionality
  - Stores a number
  - Provides a getter function
  - Implements the UUPS upgrade mechanism

- **BoxV2.sol**: Upgraded implementation
  - Adds a setter function
  - Maintains compatibility with BoxV1's storage layout
  - Implements the UUPS upgrade mechanism

### Deployment Scripts

- **DeployBox.s.sol**: Handles the initial deployment
  - Deploys the BoxV1 implementation contract
  - Deploys the ERC1967Proxy pointing to BoxV1
  - Returns the proxy address

- **upgradeBox.s.sol**: Handles the upgrade process
  - Finds the most recently deployed proxy
  - Deploys the BoxV2 implementation contract
  - Upgrades the proxy to point to BoxV2

## How the Upgrade Works

1. **Proxy Pattern**: Users interact with a proxy contract that delegates calls to an implementation contract
2. **State Separation**: All state is stored in the proxy, while logic resides in the implementation
3. **Upgrade Mechanism**: The UUPS pattern allows changing the implementation contract while keeping the same proxy address

The key function in the upgrade process is `upgradeToAndCall()`, which updates the implementation address stored in the proxy.

## Technical Details

### Storage Layout

When upgrading contracts, it's crucial to maintain the same storage layout. BoxV2 keeps the same state variable (`uint256 internal number`) to ensure compatibility.

### Authorization

The `_authorizeUpgrade` function controls who can upgrade the contract. In a production environment, this should include proper access control.

### Initialization

Instead of constructors, upgradeable contracts use initializer functions that can only be called once.

## Usage

### Deploy the Initial Version

```bash
forge script script/DeployBox.s.sol --rpc-url <your_rpc_url> --private-key <your_private_key> --broadcast
```

### Upgrade to V2

```bash
forge script script/upgradeBox.s.sol --rpc-url <your_rpc_url> --private-key <your_private_key> --broadcast
```

### Run Tests

```bash
forge test
```

## Security Considerations

- **Storage Collisions**: Always maintain the same storage layout when upgrading
- **Access Control**: Implement proper authorization for the upgrade function
- **Initialization**: Ensure initializers can only be called once
- **Transparency**: Consider implementing a timelock for upgrades in production

## Dependencies

- OpenZeppelin Contracts Upgradeable
- Foundry Development Framework

## Resources

- [OpenZeppelin Upgrades Documentation](https://docs.openzeppelin.com/upgrades-plugins/1.x/)
- [EIP-1967: Proxy Storage Slots](https://eips.ethereum.org/EIPS/eip-1967)
- [UUPS Proxies](https://docs.openzeppelin.com/contracts/4.x/api/proxy#UUPSUpgradeable)
- [Foundry Documentation](https://book.getfoundry.sh/)

