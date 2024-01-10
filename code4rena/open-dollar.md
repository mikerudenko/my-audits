# [H-1] initializeRenderer can be called by anyone and set malicious nft renderer

## Impact
The initializeManager function in the Vault721 contract lacks proper access controls. This means that any external actor can call this function and set a malicious address as the manager. This poses a significant security risk as the manager might have elevated permissions in the contract's logic, potentially allowing an attacker to manipulate the contract's state or execute unauthorized actions.

## Proof of Concept
Next function does not have any access control restriction
```solidity
  /**
   * @dev initializes NFTRenderer contract
   */
  function initializeRenderer() external {
    if (address(nftRenderer) == address(0)) _setNftRenderer(msg.sender);
  }
```

## Tools Used
Manual review

## Recommended Mitigation Steps
Implement proper access controls for the `initializeRenderer` function. Ensure that only the contract owner or a designated admin can call this function. This can be achieved using the `onlyGovernor` or a similar modifier.


# [H-2] `initializeManager` can be called by anyone and set malicious manager

## Impact
The initializeManager function in the Vault721 contract lacks proper access controls. This means that any external actor can call this function and set a malicious address as the manager. This poses a significant security risk as the manager might have elevated permissions in the contract's logic, potentially allowing an attacker to manipulate the contract's state or execute unauthorized actions.

## Proof of Concept
Next function does not have any access control restriction
```solidity
  /**
   * @dev initializes SafeManager contract
   */
  function initializeManager() external {
    if (address(safeManager) == address(0)) _setSafeManager(msg.sender);
  }
```

## Tools Used
Manual review

## Recommended Mitigation Steps
Implement proper access controls for the initializeManager function. Ensure that only the contract owner or a designated admin can call this function. This can be achieved using the `onlyGovernor` or a similar modifier.

# [M-1] In `quitSystem` function collateral can be moved to wrong _dst address with wrong collateral type

## Impact
The quitSystem function in the ODSafeManager contract allows a user to move their collateral to another destination. However, there's a critical oversight in the function: it doesn't check if the collateral type of the source matches the collateral type of the destination. This means a user can inadvertently (or maliciously) move collateral of one type to a destination expecting a different type. The destination then ends up with collateral it cannot use, potentially leading to financial loss or other unintended consequences.

## Proof of Concept
```solidity
 function quitSystem(uint256 _safe, address _dst) external safeAllowed(_safe) handlerAllowed(_dst) {
    SAFEData memory _sData = _safeData[_safe];
    ISAFEEngine.SAFE memory _safeInfo = ISAFEEngine(safeEngine).safes(_sData.collateralType, _sData.safeHandler);
    int256 _deltaCollateral = _safeInfo.lockedCollateral.toInt();
    int256 _deltaDebt = _safeInfo.generatedDebt.toInt();
    //@audit-issue missed check for source and destination collateral types
    ISAFEEngine(safeEngine).transferSAFECollateralAndDebt(
      _sData.collateralType, _sData.safeHandler, _dst, _deltaCollateral, _deltaDebt
    );

    // Remove safe from owner's list (notice it doesn't erase safe ownership)
    _usrSafes[_sData.owner].remove(_safe);
    _usrSafesPerCollat[_sData.owner][_sData.collateralType].remove(_safe);
    emit QuitSystem(msg.sender, _safe, _dst);
  }
```

The quitSystem function lacks a check to ensure that the collateral type of the source (_srcData.collateralType) matches the collateral type of the destination (_dstData.collateralType).

## Tools Used
Manual review

## Recommended Mitigation Steps
Before executing the logic inside the quitSystem function, insert a check to ensure that the collateral types match:
```solidity
SAFEData memory _dstData = _safeData[_safeDst];
if (_srcData.collateralType != _dstData.collateralType) revert CollateralTypesMismatch();
```
Alternatively, consider using the moveSAFE function if it already implements the necessary checks and logic for safely moving collateral between addresses.

## QA Report

### Q-1
**Issue:** Missing address `require` check.

- [AccountingEngine.sol#L87](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/AccountingEngine.sol#L87) on variable `_debtAuctionHouse`.
- [ODSafeManager.sol#L189](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/proxies/ODSafeManager.sol#L189) Variable `_dst`.
- [BasicActions.sol#L37](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/proxies/actions/BasicActions.sol#L37) Variables `_safeEngine` and `_safeHandler`.
- [ODSafeManager.sol#L205C32-L205C36](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/proxies/ODSafeManager.sol#L205C32-L205C36)

### Q-2
**Issue:** Unnecessary parentheses.

- [AccountingEngine.sol#L109C28-L109C43](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/AccountingEngine.sol#L109C28-L109C43)

### Q-3
**Recommendation:** Use enum instead of direct strings.

- [AccountingEngine.sol#L288](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/AccountingEngine.sol#L288)

### Q-4
**Issue:** Duplicated code.

- [CamelotRelayer.sol#L74-L84](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/oracles/CamelotRelayer.sol#L74-L84)
- [CamelotRelayer.sol#L91-L100](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/oracles/CamelotRelayer.sol#L91-L100)

**Recommendation:** Use `read` functions in `getResultWithValidity` function.

### Q-5
**Issue:** Missing inspections to check on address 0.

- [CamelotRelayerFactory.sol#L24-L25](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/factories/CamelotRelayerFactory.sol#L24-L25)

### LOW

#### L-1
**Issue:** Probable DoS vulnerability. `_safes` length can be really long.

**Recommendation:** Use `_safes` as a parameter.

- [ODSafeManager.sol#L91](https://github.com/open-dollar/od-contracts/blob/v1.5.5-audit/src/contracts/proxies/ODSafeManager.sol#L91)




