// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import 0x1::Signer;

module MyToken {
    resource T {
        coin: u64;
    }

    struct VestingSchedule {
        uint256 balance;
        uint256 startTime;
        uint256 cliff;
        uint256 duration;
    }

    mapping(address => VestingSchedule) public vestingSchedules;
    mapping(address => T) public balanceOf;

    uint256 public constant totalSupply = 10000000;
    uint256 public constant issuanceDate = 1646064000; // Example issuance date (2022-02-28)
    uint256 public constant vestingDuration = 6 * 30 days; // 6 months in seconds

    public fun main(account: &signer) {
        let signerAddr: address = Signer::address_of(account);
        let initialBalance: T;
        initialBalance.coin = totalSupply;
        balanceOf[signerAddr] = initialBalance;

        // Initialize the vesting schedule
        VestingSchedule storage vesting = vestingSchedules[signerAddr];
        vesting.balance = totalSupply;
        vesting.startTime = issuanceDate;
        vesting.cliff = issuanceDate;
        vesting.duration = vestingDuration;
    }

    function availableTokens(address account) public view returns (uint256) {
        VestingSchedule storage vesting = vestingSchedules[account];
        if (vesting.startTime == 0 || CurrentTime::now_seconds() < vesting.cliff) {
            return 0;
        } else if (CurrentTime::now_seconds() >= vesting.startTime + vesting.duration) {
            return vesting.balance;
        } else {
            return (vesting.balance * (CurrentTime::now_seconds() - vesting.startTime)) / vesting.duration;
        }
    }

    function withdrawTokens() public {
        VestingSchedule storage vesting = vestingSchedules[msg.sender];
        uint256 available = availableTokens(msg.sender);
        require(available > 0, "No tokens available for withdrawal yet");

        vesting.balance -= available;
        balanceOf[msg.sender].coin += available;
    }
}
