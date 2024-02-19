pragma solidity ^0.8.0;

contract AptosBalanceChecker {
    mapping(address => uint256) public balances;
    address[] public wealthyAccounts;
    address[] public poorAccounts;


    event BalanceUpdated(address account, uint256 balance);

   
    function updateBalance(address account, uint256 balance) internal {
        balances[account] = balance;
        emit BalanceUpdated(account, balance);
    }

   
    function checkAndDivideBalances(address[] memory accounts) external {
        for (uint i = 0; i < accounts.length; i++) {
            uint256 balance = accounts[i].balance;
            updateBalance(accounts[i], balance);
            if (balance > 10 ether) {
                wealthyAccounts.push(accounts[i]);
            } else {
                poorAccounts.push(accounts[i]);
            }
        }
    }
}
