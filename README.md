// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract MyToken {
    string public name = "MyToken";
    string public symbol = "MTK";
    uint256 public totalSupply = 10000000 * 10 ** 18; // Total supply of 10,000,000 tokens with 18 decimal places
    mapping(address => uint256) public balanceOf;
    uint256 public issuanceDate; // Date when tokens are issued
    mapping(address => uint256) public vestingSchedule; // Vesting schedule for each address

    constructor(uint256 _issuanceDate) {
        balanceOf[msg.sender] = totalSupply; // Assign all tokens to the contract creator initially
        issuanceDate = _issuanceDate;
        // Example vesting schedule: 20% of tokens released every 6 months after issuance date
        vestingSchedule[msg.sender] = totalSupply; // All tokens initially vested
    }

    // Function to calculate the amount of tokens that can be withdrawn based on the vesting schedule
    function availableTokens(address _beneficiary) public view returns (uint256) {
        if (block.timestamp < issuanceDate) {
            return 0; // Tokens are not yet issued
        }
        uint256 elapsedTime = block.timestamp - issuanceDate;
        uint256 vestedTokens = (elapsedTime / (6 * 30 days)) * (totalSupply / 5); // 20% tokens released every 6 months
        if (vestedTokens >= totalSupply) {
            return totalSupply; // All tokens are vested
        } else {
            return vestedTokens - vestingSchedule[_beneficiary]; // Tokens already withdrawn deducted
        }
    }

    // Function to withdraw available tokens
    function withdrawTokens() public {
        uint256 available = availableTokens(msg.sender);
        require(available > 0, "No tokens available for withdrawal yet");
        vestingSchedule[msg.sender] += available;
        balanceOf[msg.sender] += available;
    }
}
