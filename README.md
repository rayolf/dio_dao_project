# Atividade Avaliativa da DIO - Projeto de DAO
<br>

## Neste arquivo README.md constarão os detalhes de um projeto de DAO.


DAO (Decentralized Autonomous Organization) Project
Table of Contents
Introduction
Features
Technologies
Smart Contract Implementation
How to Run
Usage
License
Introduction
This project demonstrates the creation of a Decentralized Autonomous Organization (DAO) using Solidity smart contracts. A DAO allows for decentralized decision-making and resource allocation without centralized management. Members of the organization vote on proposals, and actions are automatically executed based on consensus.

Objective
The main goal of this project is to create a fully functioning DAO where members can:

Propose actions or decisions.
Vote on proposals.
Execute decisions automatically based on the voting outcome.
Features
Proposal Creation: Any member can create a proposal, describing an action they want the DAO to take.
Voting Mechanism: Each member has the right to vote on proposals.
Automatic Execution: Proposals are executed automatically if they receive a majority of votes.
Transparency: All actions and votes are stored on the blockchain and are publicly accessible.
Technologies
Solidity: Smart contract programming language.
Ethereum: Blockchain platform for smart contracts.
Remix IDE: Online development environment for writing, compiling, and deploying smart contracts.
Metamask: Ethereum wallet and browser extension for interacting with the blockchain.
Truffle/Hardhat: Development frameworks for testing and deploying smart contracts.
Smart Contract Implementation
Below is an example implementation of the DAO using Solidity:

solidity
Copiar código
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract DAO {
    struct Proposal {
        string description;
        uint voteCount;
        uint endTime;
        bool executed;
        mapping(address => bool) voted;
    }

    address public owner;
    uint public proposalCount;
    uint public votingDuration = 1 days;

    mapping(uint => Proposal) public proposals;
    mapping(address => bool) public members;

    modifier onlyMembers() {
        require(members[msg.sender], "Not a DAO member.");
        _;
    }

    modifier proposalExists(uint proposalId) {
        require(proposalId < proposalCount, "Proposal does not exist.");
        _;
    }

    constructor() {
        owner = msg.sender;
        members[owner] = true; // Owner is the first member
    }

    function addMember(address _member) public {
        require(msg.sender == owner, "Only owner can add members.");
        members[_member] = true;
    }

    function createProposal(string memory _description) public onlyMembers {
        Proposal storage newProposal = proposals[proposalCount];
        newProposal.description = _description;
        newProposal.endTime = block.timestamp + votingDuration;
        proposalCount++;
    }

    function vote(uint proposalId) public onlyMembers proposalExists(proposalId) {
        Proposal storage proposal = proposals[proposalId];
        require(!proposal.voted[msg.sender], "Already voted.");
        require(block.timestamp < proposal.endTime, "Voting has ended.");

        proposal.voted[msg.sender] = true;
        proposal.voteCount++;
    }

    function executeProposal(uint proposalId) public onlyMembers proposalExists(proposalId) {
        Proposal storage proposal = proposals[proposalId];
        require(!proposal.executed, "Proposal already executed.");
        require(block.timestamp >= proposal.endTime, "Voting still ongoing.");
        require(proposal.voteCount > 0, "No votes received.");

        proposal.executed = true;
        // Execute proposal logic (e.g., transferring funds, changing rules, etc.)
    }
}
Explanation:
Proposal Creation: DAO members can create proposals by providing a description. Proposals have a voting deadline (endTime).
Voting: Members can vote on proposals. Each member can only vote once per proposal.
Proposal Execution: After the voting period ends, any member can execute the proposal if it has enough votes. This triggers any predefined action related to the proposal.
How to Run
Clone the repository:

bash
Copiar código
git clone https://github.com/your-username/dao-project.git
cd dao-project
Install dependencies (if using Truffle or Hardhat):

bash
Copiar código
npm install
Compile the smart contracts:

bash
Copiar código
npx hardhat compile
Deploy the contract: You can deploy to a local blockchain or to a test network (e.g., Ropsten, Rinkeby).

bash
Copiar código
npx hardhat run scripts/deploy.js --network ropsten
Interact with the contract: Use Remix IDE or a frontend (e.g., using Web3.js) to interact with the DAO contract.

Usage
Create a Proposal: Call the createProposal function, providing a description of the proposed action.
Vote on a Proposal: Call the vote function, specifying the proposal ID.
Execute a Proposal: After the voting deadline, call executeProposal to implement the decision if it has enough votes.
License
This project is licensed under the MIT License.
