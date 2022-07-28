# multisigWallet1.sol
an advanced wallet, requires multiple confirmation before a transaction
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
//this project intends to create mutliple signatory to a wallet.
contract MultiSigWallet {
    event Deposit(address indexed sender, uint amount, uint balance);// the keyword "event" updates the frontend of our smartcontract.
    event SubmitTransaction(
        address indexed owner,
        uint indexed txIndex,
        address indexed to,
        uint value,
        bytes data
    );
    event ConfirmTransaction(address indexed owner, uint indexed txIndex);
    event RevokeConfirmation(address indexed owner, uint indexed txIndex);
    event ExecuteTransaction(address indexed owner, uint indexed txIndex);

    address[] public owners;
    mapping(address => bool) public isOwner;//if the caller of the function owns the address it would return true else it would return false
    uint public numConfirmationsRequired;// this is the number of approvals required for a withdrawal
     // if owners of the contract is three but required = 2 i.e you need 2/3 to make a transaction
    struct Transaction {
        address to;
        uint value;
        bytes data;
        bool executed;
        uint numConfirmations;
    }
     // mapping from tx index => owner => bool
    mapping(uint => mapping(address => bool)) public isConfirmed;

    Transaction[] public transactions;

    modifier onlyOwner() {
        require(isOwner[msg.sender], "not owner");
        _;
    }

