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
    }

    // Function to withdraw available tokens
    function withdrawTokens() public {
        uint256 available = availableTokens(msg.sender);
        require(available > 0, "No tokens available for withdrawal yet");
        vestingSchedule[msg.sender] += available;
        balanceOf[msg.sender] += available;
    }
}
