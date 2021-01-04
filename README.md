# Credit Module
THX Asset Pool Module used for lending purposes. 

## Interface
This section described the methods used in this module and what events should be cast to let the API pick up on the user data. 

```solidity
/* 
* Deposit()
* Checks for a mapping of the token, sets the token address in the contract, sets the caller address
* as pool borrower, checks for sufficient user balance and transfers the amount to the pool contract.
* 
* Events: 
* - LoanRequested(pool: address, borrower: address, amount: uint256, termInDays: uint256);
* Cast by the AssetPoolFactory when a borrower makes a deposit and initiates the loanpool.
*/
Deposit(token: address, amount: uint, termInDays: uint);

/* 
* Withdraw()
* Can only be called whille LoanPoolState is Pending. Withdraws the deposit (minus fees) 
* in the contract if called by the borrower.
* 
* Events: 
* - LoanRequested(pool: address, borrower: address, amount: uint256, termInDays: uint256);
* Cast by the AssetPoolFactory when a borrower makes a deposit and initiates the loanpool.
*/
Withdraw(token: address, amount: uint, termInDays: uint);

/*  
* Lend()
* Can only be called whille LoanPoolState is Pending. Checks for sufficient pool token 
* balance of the pool contract, checks if the caller is the borrower, transfers the tokens
* to the borrower. Stores the covered % of the loan per lender address. 
* 
* Events: 
* - Lent(lender: address, amount: uint256);
* Cast when a lender lends and transfers an amount to the pool.
*/
Lend(amount: uint)

/*  
* RevokeLoan()
* Can only be called whille LoanPoolState is Pending 
* 
* Events: 
* - Revoked(lender: address, amount: uint256);
* Cast when a lender revokes a (part of a) loan and transfers the amount to the lender.
*/
RevokeLoan(amount: uint);

/* 
* Borrow()
* Checks for sufficient pool token balance of the pool contract, checks if the caller is the 
* borrower, transfers the tokens to the borrower. Stores the covered % of the loan per lender 
* address.
*
* Events:
* - Borrowed(termEnd: uint);
* Cast when the loan amount is transfed to the borrower and the loan term is set.
* - Transfer(from: address, to: address, amount: uint)
*/
Borrow(amount: uint)

/* 
* Reimburse()
* Checks for sufficient balance of the borrower, calculates the reimbursement per lender and 
* transfers the funds to the given lenders.
* 
* Events: 
* - Reimbursement(lender: address, amount: uint256);
* Cast for every reimbursement made by the borrower.
* - Closed();
* Cast when the final reimbursement is made and the LoanPoolState moves to closed.
*/
Reimburse(amount: uint)
```

## Storage
This section lists the properties used by the methods in this module.

```solidity
/* 
* Pending: Open for lenders to participate in the loan.
* Active: Transferring for reimbursements.
* Closed: Loans are reimbursed before the loan term end.
* Dispute: Loans are not reimbursed before loan term end.
*/
enum LoanPoolState { Pending, Active, Closed, Dispute};

// State property reflecting the phase of the loan pool.
state: LoanPoolState;

// The max loan calculated with the fee % parameters and the size of the initial deposit
loanLimit: uint; 

// The end term timestamp for the loans to be reimbursed
endTerm: uint;

// The address of the borrower 
borrower: address; 

// The lend amounts per lender
mapping(address => amount) loans;
```

## Reputation
TODO: How are reputation metrics calculated based on the user data exposed through events.

Example:
* Credit Worthy: Paid back 100% of the loans within the agreed term.
* Promoter: Gets loans covered in a short amount of time. 
* Liquid: Covers the largest loans when participating in loan pools.

## Governance
TODO: What governance parameters are used in this module?