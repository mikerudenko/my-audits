[H-1]

## Impact

The identified issue pertains to the method by which rewards are distributed to token holders in the LiquidInfrastructureERC20 contract. Specifically, the problem arises when rewards are distributed to a subset of holders rather than the entire set at the end of a distribution period. Since the contract allows for distributing rewards to a specific number of users at a time, there is a potential for an uneven distribution of rewards. This is due to the LastDistribution block number only being updated after rewards have been distributed to the last holder in the list, which does not accurately reflect the distribution period for all holders.

In scenarios where distribution is performed on a subset of holders after the expected distribution block, those early recipients receive rewards for extra blocks not accounted for in the original distribution plan (e.g., receiving rewards for 12 blocks instead of the planned 10). Conversely, holders who receive their distribution later do not receive rewards for the interim blocks between their distribution and the last update of LastDistribution. This creates a scenario where rewards are not distributed equitably among all token holders.

## Proof of Concept

Given the scenario you described, let's illustrate the issue and propose a solution with an example and an ASCII schema.

Example Scenario
Let's assume a reward period is every 10 blocks, and there are 10 holders. Ideally, every holder should receive rewards after every 10 blocks. However, due to the flexibility in the distribution mechanism allowing partial distributions, the timing of reward updates causes an inequitable distribution of rewards.

Initial Setup:
Reward period: Every 10 blocks
Total holders: 10 (numbered 1 to 10)
Distribution starts at block 10 but is executed at block 12 for the first 2 holders.
What Happens:
At block 12, distribute rewards to holders 1 and 2.
At block 14, distribute to the next 2 holders (3 and 4), and so on.
LastDistribution updates to block 20 after the last set of holders receive their rewards, neglecting the period mismatch for each holder.
ASCII Schema of the Issue:

```
Blocks:      10    12    14    16    18    20
            |      |     |     |     |     |
Holders:   -----------------------------------
1-2        |------X-----|-----|-----|-----|
3-4        |      |-----X-----|-----|-----|
5-6        |      |     |-----X-----|-----|
7-8        |      |     |     |-----X-----|
9-10       |      |     |     |     |-----X
```

X marks when the reward is distributed to each pair of holders.
Holders 1-2 receive their rewards at block 12 but are effectively getting rewards for 2 extra blocks not covered in the original period.
Holders 9-10, receiving rewards last, do not get compensated for the rewards from blocks 11-20 correctly due to the staggered distribution.

## Tools Used

Manual

## Recommended Mitigation Steps

Solution Concept:
Implement a holder-specific LastDistribution marker that tracks the last block at which each holder received their reward, ensuring equitable distribution based on actual holding periods.

ASCII Schema of the Solution:

```
Blocks:      10    12    14    16    18    20
            |      |     |     |     |     |
Holders:   -----------------------------------
1-2        |------X     |     |     |     |
3-4        |      |-----X     |     |     |
5-6        |      |     |-----X     |     |
7-8        |      |     |     |-----X     |
9-10       |      |     |     |     |-----X
```

Each X represents the distribution point, adjusted for equitable rewards.
Implementation:
Track Last Distribution Per Holder: Create a mapping (e.g., mapping(address => uint256) private lastDistributionBlock;) to keep track of the last block at which each holder was rewarded.
Calculate Rewards Equitably: When calculating rewards for a holder, consider the difference between the current block and their lastDistributionBlock to determine the exact reward amount.
Update on Distribution: After distributing rewards to a holder, update their lastDistributionBlock to the current block number.

[H-2]

## Impact
The issue within the smart contract code has a substantial impact on the functionality and reliability of the system. Specifically, the use of the `transfer ` method for distributing tokens to holders poses a risk of a Denial of Service (DoS) attack under certain conditions. Since the protocol supports the use of tokens like USDC and USDT, which incorporate blacklist mechanisms, there is a scenario where a holder might be blacklisted after receiving tokens. In such cases, the transfer method would revert, effectively halting the distribution process to subsequent recipients. This not only disrupts the intended functionality of distributing tokens but also exposes the system to potential manipulation, as attackers could deliberately exploit this vulnerability to prevent the distribution of rewards or payments.

## Proof of Concept
The issue is present in the LiquidInfrastructureERC20.sol contract, specifically at the following line of code:
```
if (toDistribute.transfer(recipient, entitlement)) {
    receipts[j] = entitlement;
}
```
This snippet demonstrates the transfer of tokens to a recipient, with a comment hinting at the potential for a "weird" behavior or issue with token transfers.

## Tools Used
Manual

## Recommended Mitigation Steps
Adopt the Pull Over Push Pattern: Modify the contract to allow recipients to withdraw their tokens themselves instead of pushing tokens to them directly. This pattern is more resilient to individual account issues and reduces the risk of impacting the entire distribution process.

Use Low-Level Calls for Token Transfers: Instead of using the transfer method, consider using low-level call methods with proper checks to handle token transfers. This approach provides more control and can prevent unintentional reverts if a recipient is on a blocklist.







[M-1]

## Impact

The contract calculates rewards based on the presence of tokens at the time of distribution without considering the duration for which the tokens were held during the distribution period. This mechanism does not account for tokens acquired shortly before the distribution, allowing new token holders to receive a full period's reward unfairly.
This vulnerability impacts the fairness and integrity of the reward system, potentially leading to manipulation by users who might acquire tokens shortly before distributions to gain undue rewards. It disincentivizes long-term holding and undermines the value proposition for genuine investors.

## Proof of Concept

The critical function that checks whether the minimum distribution period has passed is \_isPastMinDistributionPeriod. The issue arises because this function only checks the block number to determine if the minimum period has elapsed since the last distribution, without accounting for the duration of token holding:

```
function _isPastMinDistributionPeriod() internal view returns (bool) {
    if (totalSupply() == 0 || holders.length == 0) {
        return false;
    }
    return (block.number - LastDistribution) >= MinDistributionPeriod;
}

```

## Tools Used

Manual

## Recommended Mitigation Steps

Track Token Holding Duration: Implement a mechanism to track the duration for which each token has been held. This could involve mapping addresses to holding periods or leveraging checkpoints.

Prorate Rewards Based on Holding Period: Adjust the reward calculation to prorate rewards based on the actual holding period during the distribution cycle. For example, if the distribution period is 30 days and a holder has had the tokens for 15 days, they should receive only 50% of the reward they would have earned for holding throughout the entire period.

Implement a Vesting Mechanism for Newly Minted Tokens: Consider implementing a vesting mechanism for rewards on newly minted tokens. This can ensure that rewards are distributed more fairly, based on the time each token was held during the distribution period.

Use a Decentralized Finance (DeFi) Protocol Model: Look into existing DeFi protocols that have solved similar issues for inspiration. Many have intricate systems for calculating rewards based on token holding duration and participation.
