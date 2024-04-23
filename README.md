
# Teller Finance contest details

- Join [Sherlock Discord](https://discord.gg/MABEWyASkp)
- Submit findings using the issue page in your private contest repo (label issues as med or high)
- [Read for more details](https://docs.sherlock.xyz/audits/watsons)

# Q&A

### Q: On what chains are the smart contracts going to be deployed?
Ethereum Mainnet, Sepolia Testnet, Arbitrum One, Base, Polygon, Clarity (Conduit Layer 3 on top of Base), 
___

### Q: If you are integrating tokens, are you allowing only whitelisted tokens to work with the codebase or any complying with the standard? Are they assumed to have certain properties, e.g. be non-reentrant? Are there any types of <a href="https://github.com/d-xo/weird-erc20" target="_blank" rel="noopener noreferrer">weird tokens</a> you want to integrate?
We are allowing any standard token that would be compatible with Uniswap V3 to work with our codebase, just as was the case for the original audit of TellerV2.sol .  The tokens are assumed to be able to work with Uniswap V3 .  
___

### Q: Are the admins of the protocols your contracts integrate with (if any) TRUSTED or RESTRICTED? If these integrations are trusted, should auditors also assume they are always responsive, for example, are oracles trusted to provide non-stale information, or VRF providers to respond within a designated timeframe?
n/a
___

### Q: Are there any protocol roles? Please list them and provide whether they are TRUSTED or RESTRICTED, or provide a more comprehensive description of what a role can and can't do/impact.
The owner of TellerV2.sol (TRUSTED) is able to pause or unpause the protocol. When paused, new loans cannot be submitted or fulfilled via submitBid or via lenderAcceptLoan as those fn calls will revert.  They should not be able to steal funds or interfere with loans in other ways.  (This note is not really relevant for this audit since we covered this in the first audit but I added it here anyways) .

The LenderGroup_Smart contract will not only be deployed once, but will be deployed in many iterations since it is a 'pool'.   The owner (RESTRICTED) of any given LenderGroup_Smart contract is able to freeze or unfreeze borrowing (the capability to borrow money from) the smart contract.  They should have no other special privileges, should not be able to steal funds, and it should be possible to revoke this privilege and burn the owner address.
___

### Q: For permissioned functions, please list all checks and requirements that will be made before calling the function.
LenderCommitmentGroupShares can only be minted or burned by the Owner (owner will always be LenderGroups_Smart in our ecosystem -- one shares token per one lender groups contract) .  

LenderCommitmentGroupSmart.sol.acceptFundsForAcceptBid can only be called by the SmartCommitmentForwarder.  

LenderCommitmentGroupSmart.sol.repayloanCallback can only be called by TellerV2.sol  .

FlashRolloverLoan_G5.executeOperation can only be called by the FlashLoanPool (aave contract) as it is the flash loans callback fn . 
___

### Q: Is the codebase expected to comply with any EIPs? Can there be/are there any deviations from the specification?
No unusual EIPs; only ERC20 for the lender group shares token.  (Strictly)  
___

### Q: Are there any off-chain mechanisms or off-chain procedures for the protocol (keeper bots, arbitrage bots, etc.)?
There are not off-chain mechanisms or procedures such as keeper bots at this time.  
___

### Q: Are there any hardcoded values that you intend to change before (some) deployments?
I intend to add events and event emits before some deployments, specifically to the Lender Group Smart contract. (no logic changes besides bug fixes). 
___

### Q: If the codebase is to be deployed on an L2, what should be the behavior of the protocol in case of sequencer issues (if applicable)? Should Sherlock assume that the Sequencer won't misbehave, including going offline?
For now, assume the sequencer will not misbehave or go offline.  Our protocol does not directly deal with bridging.  
___

### Q: Should potential issues, like broken assumptions about function behavior, be reported if they could pose risks in future integrations, even if they might not be an issue in the context of the scope? If yes, can you elaborate on properties/invariants that should hold?
n/a
___

### Q: Please discuss any design choices you made.
We do want Lender Groups pools to remain stable over long periods of time such that a lender can deposit funds in, wait many months or years, and then withdraw the funds with the expectation that they will get the funds back + interest earned on loans.  It is known that if (loans default AND collateral values drop significantly) then it is possible for the lenders to lose money but there should be no exploitatious ways for them to lose their deposited money outside of that particular combination of events.   


___

### Q: Please list any known issues/acceptable risks that should not result in a valid finding.
We chose to ignore MEV in the lender group contract for now as it was too difficult to design out and because other pools such as Uniswap exist with MEV.  In this context, MEV means that a lender that is depositing / withdrawing shares to and from the lender group contract can deposit and withdraw in such a way as to do it in a sandwich effectively playing around  the valuation of a share.   As far as I know, this MEV 'exploit' also exists in uniswap pools and is largely insignificant for general intents and purposes.
___

### Q: We will report issues where the core protocol functionality is inaccessible for at least 7 days. Would you like to override this value?
no
___

### Q: Please provide links to previous audits (if any).
Only past audit was by Sherlock
https://audits.sherlock.xyz/contests/62
___

### Q: Please list any relevant protocol resources.
website: http://teller.org

docs: https://docs.teller.org/teller-lite
___

### Q: Additional audit information.
One area of interest to look in to is the behavior of the value of shares over time in the LenderGroups_Smart contract.   As loans are taken out and then repaid or liquidated upon, the variables tracking the total principal available for lending and the total amount of principal repaid and the value of each share should remain ‘stable’ and should not be able to diverge in a state that causes loss of funds or permanent reverting (lock/freeze of contract) etc.   This is because the goal of this smart contract is to allow lenders to deposit funds which will be borrowed by lenders and repaid with interest.  The lender will then have indirectly earned that interest since the value of their shares has increased.   
___



# Audit scope


[teller-protocol-v2-audit-2024 @ 35b31ef241fe1c3116ab4e5d3da9abc317b949cf](https://github.com/teller-protocol/teller-protocol-v2-audit-2024/tree/35b31ef241fe1c3116ab4e5d3da9abc317b949cf)
- [teller-protocol-v2-audit-2024/packages/contracts/contracts/LenderCommitmentForwarder/SmartCommitmentForwarder.sol](teller-protocol-v2-audit-2024/packages/contracts/contracts/LenderCommitmentForwarder/SmartCommitmentForwarder.sol)
- [teller-protocol-v2-audit-2024/packages/contracts/contracts/LenderCommitmentForwarder/extensions/FlashRolloverLoan_G5.sol](teller-protocol-v2-audit-2024/packages/contracts/contracts/LenderCommitmentForwarder/extensions/FlashRolloverLoan_G5.sol)
- [teller-protocol-v2-audit-2024/packages/contracts/contracts/LenderCommitmentForwarder/extensions/LenderCommitmentGroup/LenderCommitmentGroupShares.sol](teller-protocol-v2-audit-2024/packages/contracts/contracts/LenderCommitmentForwarder/extensions/LenderCommitmentGroup/LenderCommitmentGroupShares.sol)
- [teller-protocol-v2-audit-2024/packages/contracts/contracts/LenderCommitmentForwarder/extensions/LenderCommitmentGroup/LenderCommitmentGroup_Smart.sol](teller-protocol-v2-audit-2024/packages/contracts/contracts/LenderCommitmentForwarder/extensions/LenderCommitmentGroup/LenderCommitmentGroup_Smart.sol)
- [teller-protocol-v2-audit-2024/packages/contracts/contracts/TellerV2.sol](teller-protocol-v2-audit-2024/packages/contracts/contracts/TellerV2.sol)
- [teller-protocol-v2-audit-2024/packages/contracts/contracts/TellerV2MarketForwarder_G2.sol](teller-protocol-v2-audit-2024/packages/contracts/contracts/TellerV2MarketForwarder_G2.sol)
- [teller-protocol-v2-audit-2024/packages/contracts/contracts/TellerV2MarketForwarder_G3.sol](teller-protocol-v2-audit-2024/packages/contracts/contracts/TellerV2MarketForwarder_G3.sol)

