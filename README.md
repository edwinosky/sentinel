# Sentinel


Sentinel is an all-powerful.

Sentinel is an autonomous agent for persisting, processing and automating governance, objects, and tasks

Sentinel is implemented as a Python application that binds Masternode.

This guide covers installing Sentinel onto Masternode in Ubuntu 14.04 / 16.04.


    cd .safecrex   // Adjust according to your root SafeCrex directory path

    ./safecrex-cli stop

    rm mncache.dat

    rm mnpayments.dat

    ./safecrexd -reindex



## Installation

### 1. Install Prerequisites

Make sure Python version 2.7.x or above is installed:

    python --version

Update system packages and ensure virtualenv is installed:

    $ sudo apt-get update
    $ sudo apt-get -y install python-virtualenv


### 2. Install Sentinel

Clone the Sentinel repo and install Python dependencies.

    $ git clone https://github.com/edwinosky/sentinel.git && cd sentinel
    $ virtualenv ./venv
    $ ./venv/bin/pip install -r requirements.txt


### 3. Configure & Test Your Configuration

Open sentinel.conf - Run the following command in linux:

    $ nano sentinel.conf

Adjust the path to your Masternode’s safecrex.conf. Save the file then close it.

    vivo_conf=/path/to/safecrex.conf

Now run:

    $ venv/bin/python bin/sentinel.py

You should see: “coind not synced with network! Awaiting full sync before running Sentinel.”
This is exactly what we want to see at this stage.

If the wallet has been resynched alreaedy, you will see no output which is what you want to see and it means you can skip the next sync step.


## 4. Check That Your Wallet is Synced 

Go back into your root Vivo directory, then check the status of your sync:

    cd .. 
    ./safecrex-cli mnsync status


This is what you’re waiting to see:

AssetId 999, all trues, one false, and a FINISHED. Keep issuing ./safecrex-cli mnsync status until it looks like this:


    {
    “AssetID”: 999,
    “AssetName”: “MASTERNODE_SYNC_FINISHED”,
    “Attempt”: 0,
    “IsBlockchainSynced”: true,
    “IsMasternodeListSynced”: true,
    “IsWinnersListSynced”: true,
    “IsSynced”: true,
    “IsFailed”: false
    }
    
At this point, your remote masternode is synchronized and chatting with the network but is not accepted as a masternode because it hasn’t been introduced to the network by your collateral.


## 5. Start Your Masternode

 Go back to your local wallet, open the debug console, and run these commands to start your masternode (LABEL is the name you used for your MN in the masternode.conf):

    walletpassphrase <YOURPASSPHRASE> 120 (only if you have a wallet password)
    masternode start-alias <LABEL>


## 6. Test Your Sentinel

You’re needed back in Sentinel directory:

    cd sentinel

Run:

    venv/bin/python bin/sentinel.py

It should return no output if everything is working correctly. This is how you know it’s working, and your masternode and sentinel setup is properly configured.

## 7. Create Your Sentinel Crontab Entry

Run:

    crontab -e

Add the following line below to the end of the file:

    * * * * * cd /home/YOURUSERNAME/.safecrexcore/sentinel && ./venv/bin/python bin/sentinel.py 2>&1 >> sentinel-cron.log
    

Make sure you:

1) Change USERNAME to your username.
2) Hit enter to create another line at the end after this line, or the file will not work.

Save and exit.

## 8. All Done On Sentinel. Finally Check Your Masternode

Go back into your Safecrex root directory:

    cd ..

Run:

    ./safecrex-cli masternode status

You should see the message “Masternode successfully started.”. If you have followed all the steps outlined in the guide accurately and achieved this result - this is it, you've made it. Congratulations!

## Troubleshooting

To view debug output, set the `SENTINEL_DEBUG` environment variable to anything non-zero, then run the script manually:

    $ SENTINEL_DEBUG=1 ./venv/bin/python bin/sentinel.py

