# DecentralizedMarketing-Web3-Based-Influencer-Marketing-Platform
DecentralizedMarketing connects brands with influencers and manages campaign payments and performance tracking using smart contracts, ensuring transparency and trust in influencer marketing.
from web3 import Web3
from solcx import compile_source

# Mock Smart Contract in Solidity
# This is a simplified version and won't run as is. In a real scenario, you would deploy this to a blockchain.
contract_source_code = '''
pragma solidity ^0.8.0;

contract InfluencerMarketing {
    struct Campaign {
        address brand;
        address influencer;
        uint256 budget;
        bool completed;
    }
    
    mapping(uint => Campaign) public campaigns;
    uint public campaignsCount;

    function createCampaign(address influencer, uint256 budget) public {
        campaigns[campaignsCount] = Campaign(msg.sender, influencer, budget, false);
        campaignsCount++;
    }
    
    function markCampaignCompleted(uint campaignId) public {
        Campaign storage campaign = campaigns[campaignId];
        require(msg.sender == campaign.brand, "Only the brand can complete the campaign.");
        campaign.completed = true;
    }
    
    function getCampaign(uint campaignId) public view returns (address brand, address influencer, uint256 budget, bool completed) {
        Campaign storage campaign = campaigns[campaignId];
        return (campaign.brand, campaign.influencer, campaign.budget, campaign.completed);
    }
}
'''

# Mock for Web3.py setup - This would typically connect to an actual Ethereum network
w3 = Web3(Web3.EthereumTesterProvider())

# Compiling contract (mock)
compiled_sol = compile_source(contract_source_code)
contract_interface = compiled_sol['<stdin>:InfluencerMarketing']

# Deploying contract (mock)
InfluencerMarketing = w3.eth.contract(abi=contract_interface['abi'], bytecode=contract_interface['bin'])
tx_hash = InfluencerMarketing.constructor().transact()
tx_receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
influencer_marketing = w3.eth.contract(
    address=tx_receipt.contractAddress,
    abi=contract_interface['abi'],
)

# Mock functions to interact with the contract
def create_campaign(influencer_address, budget):
    tx_hash = influencer_marketing.functions.createCampaign(influencer_address, budget).transact()
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    return receipt

def complete_campaign(campaign_id):
    tx_hash = influencer_marketing.functions.markCampaignCompleted(campaign_id).transact()
    receipt = w3.eth.wait_for_transaction_receipt(tx_hash)
    return receipt

def get_campaign(campaign_id):
    return influencer_marketing.functions.getCampaign(campaign_id).call()

# Example Usage
influencer_address = w3.eth.account.create().address # Simulated influencer address
budget = 1000  # Example budget

# Creating a campaign
receipt = create_campaign(influencer_address, budget)
print("Campaign created with transaction receipt:", receipt)

# Completing a campaign
complete_campaign(0) # Assuming 0 is the ID of the first campaign

# Getting campaign details
campaign_details = get_campaign(0)
print("Campaign details:", campaign_details)
