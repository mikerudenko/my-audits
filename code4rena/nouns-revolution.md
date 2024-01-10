## [H-1]

### Impact
The `AuctionHouse.sol` contract in its current form is susceptible to block stuffing attacks. This vulnerability arises from the way the auction extension logic is implemented. The condition `bool extended = _auction.endTime - block.timestamp < timeBuffer;` checks if the auction can be extended based on the `timeBuffer`. However, without a minimum limit on `timeBuffer`, an attacker can economically benefit from manipulating block times to repeatedly extend the auction. This can lead to unfair auction practices and potential manipulation of auction outcomes.

### Proof of Concept
- **Location of Issue**: [AuctionHouse.sol#L191](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/AuctionHouse.sol#L191)
- **TimeBuffer Setting**: [AuctionHouse.sol#L136](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/AuctionHouse.sol#L136)
- **Current Implementation**:
  The `timeBuffer` is set without a minimum limit, allowing for the possibility of setting it to a very low value. This can be exploited through block stuffing, where an attacker artificially extends the auction duration by manipulating block times.
This article explain block stuffing attack in [details](https://medium.com/hackernoon/the-anatomy-of-a-block-stuffing-attack-a488698732ae)

### Tools Used
- Manual review of the smart contract code.

### Recommended Mitigation Steps
- Implement a minimum limit for `timeBuffer` during its setting in the contract. This limit should be chosen based on economic considerations to ensure that extending the auction via block stuffing is not profitable.
- The code for setting `timeBuffer` should include a check like:
  ```solidity
  require(newTimeBuffer >= minimumTimeBuffer, "TimeBuffer is too low");

## [H-2]: Infeasibility of Retrieving Art Piece by Current Verb ID in `VerbsToken.sol`

### Impact
The `getArtPieceByID` function in the `VerbsToken.sol` contract is designed to retrieve art pieces based on their Verb ID. However, due to the way Verb IDs are assigned and incremented in the `mintTo` function, it is impossible to retrieve an art piece using the current Verb ID (`_currentVerbId`). The issue arises because the Verb ID is assigned first and then `_currentVerbId` is incremented. As a result, the check `require(verbId <= _currentVerbId, "Invalid piece ID");` in `getArtPieceByID` will always fail if passed verbId is equal _currentVerbId, breaking the functionality of retrieving the latest art piece by its ID.

### Proof of Concept
- **Location of Issue**: [VerbsToken.sol#L273-L276](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/VerbsToken.sol#L273-L276)
- **Minting Function**: [VerbsToken.sol#L294](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/VerbsToken.sol#L294)
- **Current Implementation**:
  In the `mintTo` function, `uint256 verbId = _currentVerbId++` assigns the current Verb ID to a new art piece and then increments `_currentVerbId`. This means `_currentVerbId` is always one more than the highest assigned Verb ID.

### Tools Used
- Manual review of the smart contract code.

### Recommended Mitigation Steps
- Update the conditional check in `getArtPieceByID` to allow retrieval of the art piece with the highest assigned Verb ID. The updated condition should be:
  ```solidity
  require(verbId < _currentVerbId, "Invalid piece ID");
  ```

### [M-1]
The absence of a check for a zero address in the `creatorAddress` during the initialization of the `ERC20TokenEmitter` contract can lead to a critical malfunction in the `_mint` logic. If `creatorAddress` is inadvertently set to the zero address (0x0), the intended minting process to the creator's address will fail. This issue breaks the main invariant of the minting function, potentially leading to a failure in token distribution and affecting the core functionality of the contract.

### Proof of Concept
- **Location of Issue**: [ERC20TokenEmitter.sol#L201](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/ERC20TokenEmitter.sol#L201)
- **Initialization Function**: [ERC20TokenEmitter.sol#L91-L105](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/ERC20TokenEmitter.sol#L91-L105)

In the provided code, the `creatorAddress` can be set during the initialization process. However, there is no explicit check to ensure that this address is not the zero address. This oversight can lead to scenarios where the `_mint` function does not operate as intended, as it relies on `creatorAddress` being a valid, non-zero address.

### Tools Used
- Manual review of the smart contract code.

### Recommended Mitigation Steps
- Implement a validation check in the initialization function to ensure that `creatorAddress` is not set to the zero address. This can be done using a require statement such as `require(creatorAddress != address(0), "Creator address cannot be the zero address");`.
- Ensure that this validation is applied in any function or process where `creatorAddress` can be set or modified.
- Adding such a check will prevent potential failures in the minting process and uphold the contract's integrity.

## [M-2] Incorrect Boundary Comparison in `RewardSplits.sol` Leads to Unintended Function Reversion

### Impact
In the `RewardSplits.sol` contract, the current implementation of boundary value comparison for `paymentAmountWei` can lead to unintended behaviour. Specifically, if `paymentAmountWei` is exactly equal to either `minPurchaseAmount` or `maxPurchaseAmount`, the function will incorrectly revert. This strict inequality check excludes valid boundary values, potentially disrupting normal contract operations and preventing legitimate transactions that exactly match the boundary conditions.

### Proof of Concept
- **Location of Issue**: [RewardSplits.sol#L41](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/protocol-rewards/src/abstract/RewardSplits.sol#L41)
- **Current Implementation**:
```solidity
  if (paymentAmountWei <= minPurchaseAmount || paymentAmountWei >= maxPurchaseAmount) revert INVALID_ETH_AMOUNT();
```

### Tools used
Manual review

### Recommended Mitigation Steps
Modify the conditional check to allow paymentAmountWei to be equal to minPurchaseAmount and maxPurchaseAmount. The updated condition should be:
  ```solidity
if (paymentAmountWei < minPurchaseAmount || paymentAmountWei > maxPurchaseAmount) revert INVALID_ETH_AMOUNT();
  ```

### Gas Optimisation Report

#### Low Issues:

**[L-1]:**
- **Location**: [CultureIndex.sol#L138](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/CultureIndex.sol#L138)
- **Summary**: Add a check to ensure `_cultureIndexParams.minVoteWeight` is greater than 0. This validation is crucial for maintaining the integrity of the voting mechanism and avoiding potential issues with zero or negative weights.

**[L-2]:**
- **Location**: [TokenEmitterRewards.sol#L18](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/protocol-rewards/src/abstract/TokenEmitter/TokenEmitterRewards.sol#L18)
- **Summary**: The inspection `if (msgValue < computeTotalReward(msgValue)) revert INVALID_ETH_AMOUNT();` is redundant. The `computeTotalReward` function returns 2.5% of the passed value, which will always be less than `msgValue`. Suggestion is to remove this code to optimize gas usage.

#### Non-critical:

**[N-1]:**
- **Location**: [AuctionHouse.sol#L278](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/AuctionHouse.sol#L278)
- **Summary**: Bound the `timeBuffer` value to prevent extreme or invalid configurations that could affect auction timing and efficiency.

**[N-2]:**
- **Location**: [AuctionHouse.sol#L288](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/AuctionHouse.sol#L288)
- **Summary**: Bound the `reservePrice` value to ensure it remains within reasonable and manageable limits, enhancing the stability and predictability of the auction process.

**[N-3]:**
- **Location**: [AuctionHouse.sol#L298](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/AuctionHouse.sol#L298)
- **Summary**: Bound the `minBidIncrementPercentage` to prevent excessively high or low increments, which could lead to inefficient auction dynamics.

**[N-4]:**
- **Location**: [CultureIndex.sol#L134](https://github.com/code-423n4/2023-12-revolutionprotocol/blob/d42cc62b873a1b2b44f57310f9d4bbfdd875e8d6/packages/revolution/src/CultureIndex.sol#L134)
- **Summary**: Bound `_cultureIndexParams.erc721VotingTokenWeight` to ensure it stays within practical limits, maintaining a balanced and fair voting system.

