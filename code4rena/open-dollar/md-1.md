# In `quitSystem` function collateral can be moved to wrong _dst address with wrong collateral type

## Impact
The quitSystem function in the ODSafeManager contract allows a user to move their collateral to another destination. However, there's a critical oversight in the function: it doesn't check if the collateral type of the source matches the collateral type of the destination. This means a user can inadvertently (or maliciously) move collateral of one type to a destination expecting a different type. The destination then ends up with collateral it cannot use, potentially leading to financial loss or other unintended consequences.

## Proof of Concept
```
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
```
SAFEData memory _dstData = _safeData[_safeDst];
if (_srcData.collateralType != _dstData.collateralType) revert CollateralTypesMismatch();
```
Alternatively, consider using the moveSAFE function if it already implements the necessary checks and logic for safely moving collateral between addresses.


