<img src="https://raw.githubusercontent.com/juanjux/raimix/master/img/logo.png"
 alt="RaiMixer logo" title="RaiMixer" align="right" width=180px />

# NanoMixer: local transaction scrambler for Nano

<img src="https://raw.githubusercontent.com/juanjux/raimix/master/img/gui.png"
 alt="RaiMixer GUI" title="RaiMixer" width=640px />

## IMPORTANT

This is not finished. It needs more tests, documentation, and more
anonymity features.

**It could also could eat your funds alive and leave you poor and homeless.
You've been warned.**

## What is this?

This is a fund mixer/shaker to improve anonymity on
[Nano](https://nano.org) transactions. It will create a (configurable)
number of mixer accounts and send the amount between them and the
source account in several (configurable) mixing rounds until finally all the
funds are sent to the destination account.

This takes advantage of the fast and feeless transactions of the awesome
[Nano Cryptocurrency](https://nano.org).

## Is this safe?

The answer depends on your definition of safe. This probably won't protect you from a well-done blockchain analysis by professionals. But since they can only get a
certain % of certainty, that lowers as you increase the number of mixing rounds
and accounts, it's very improbable that it could be used as solid evidence in a
trial, for example. That is why we recommend that you increase the number of mixing roudns and accounts if possible to make the process safer.

In case you missed the warning in the first section, please note that this is
currently a very early release and some things will improve and there could be
bugs.Feel free to reach out 

## Won't the nodes know where the amounts are coming from because of my IP?

No, nodes don't have a way to know if a block they're receiving is being
originated in a peer or simply propagated.

## Is this safer than an online mixer?

Not really. The advantage of an online mixer like
[RaiShaker](https://raishaker.net/) is that it mixes amounts from different
users. So even while nodes can't know if different accounts are from the same or
different persons, a comprehensive analysis could see if all the funds from the
first transactions before mixing came from known exchanges accounts and thus can
guess that the transactions following that one are to the mixers accounts.
RaiShaker avoids that problem by mixing the funds from its users together.

But this also has some advantages over an online mixer:

- You don't have the trust the online mixer operator (that will know where the
  funds came from and where they'll go).

- This is probably faster since the online mixers have to wait until they have a
  minimum amount of users to start the mixing process.

To be double safe you could use RaiMixer before sending it to RaiShaker.

## How much time does it take?

The bottleneck is the small POW done on every transaction and the fact that, at
least on my tests, the node doesn't seem to parallelize RPC requests even if
it's configured to use several threads or receive them on different connections.
So it'll depend on the number of transactions, which in turn will depend on the
number of mixing accounts and mixing rounds configured.

This number of transactions can't be exactly predicted because the program
randomized some things, but a typical 4-accounts, 2 rounds mixing produces about
50 transactions, which on my machine take about 5 minutes to complete.

## Installation

```bash
pip install raimixer
pip install pyqt5 # only if you want to use the GUI
```

## How to use

Note: [**Python 3.6 required**](https://www.python.org/downloads/release/python-364/).

First, edit your `~/RaiBlocks/config.json` and set to `"true"` the settings called
`enable_control` and `rpc_enable`. Then close and reopen your wallet or node
and unlock it. It must remain running while this script runs. The program will
remind you if either of these things is not done.

The program can be used in command line or GUI modes. For the GUI, just run:

```
raimixer --gui
```

The simplest example of the command line mode would be:

```bash
raimixer <destination_account> <ammount>
```

This will use the default wallet and accounts as specified in RaiBlock's
`config.json` file. You can use the options `--wallet` and `--source_acc` to
set specific ones if you want.

The amount must end with "mrai" for megarais or "krai" for kilorais. Smaller amounts
are not supported at the moment (but you can use decimals).

Another interesting option that you could want to consider is
`--initial_amount`. This will make the mixing send the specified bigger amount
than the one needed to make analysis harder; the excess amount will be
returned to the source account at the end of the process.

Mixing accounts will be deleted at the end of the operation if everything
worked.

If the RaiBlocks node or wallet crashes during the operation (which sometimes
happens), you can use the `--clean` option to recover all amounts in all the
accounts except the one you set as `--source_acc`. Please note that this can't
make out mixing accounts from previous sessions from user-created accounts, so
if you want to keep some amount in other accounts you'll have to move the
fund manually (or run this and then restore the funds later, `--clean` won't
delete any account.)

## Other options

```bash

raimixer --help

usage: raimixer [-h] [-w WALLET] [-s SOURCE_ACC] [-c] [-d] [-i INITIAL_AMOUNT]
                [-m] [-l] [-n NUM_MIXERS] [-r NUM_ROUNDS] [-u RPC_ADDRESS]
                [-p RPC_PORT] [-g]
                [dest_acc] [amount]

  _   _                     __  __ _               
 | \ | |                   |  \/  (_)              
 |  \| | __ _ _ __   ___   | \  / |___  _____ _ __
 | . ` |/ _` | '_ \ / _ \  | |\/| | \ \/ / _ \ '__|
 | |\  | (_| | | | | (_) | | |  | | |>  <  __/ |   
 |_| \_|\__,_|_| |_|\___/  |_|  |_|_/_/\_\___|_|   

Mix/scramble Nano transactions between random local accounts before
sending to the real destination.

Example usage:

raimixer xrb_3zq1yrhgij8ix35yf1khehzwfiz9ojjotndtqprpyymixxwxnkhn44qgqmy5 10xrb

If this software is useful to you, consider donating to the author's rai-funds!

xrb_3usnd3kirzfudprd3tceauh3sejxpfm754jgnjajbttrefx9obgdqe69wfcf

(Thank you!❤)

positional arguments:
  dest_acc              Destination account (mandatory except on --consolidate)
  amount                Amount. Use xrb/mrai or krai sufixes for mega/kilo rai (mandatory except for --consolidate)

optional arguments:
  -h, --help            show this help message and exit
  -w WALLET, --wallet WALLET
                        User wallet ID (default: from Rai config)
  -s SOURCE_ACC, --source_acc SOURCE_ACC
                        Source account (default: from Rai config)
  -c, --consolidate     Move everything to the source account. Useful after node crashes.
  -d, --delete_empty    Delete empty accounts
  -i INITIAL_AMOUNT, --initial_amount INITIAL_AMOUNT
                        Initial amount to mix. Helps masking transactions. Must be greater
                        than "amount". Rest will be returned to source account (default: equal to "amount")
  -m, --dest_from_multiple
                        Send to the final destination from various mixing account
  -l, --leave_remainder
                        Leave excess amount in the mixing accounts (don't return to main account at the end)
  -n NUM_MIXERS, --num_mixers NUM_MIXERS
                        Number of mixing accounts to create (default=4)
  -r NUM_ROUNDS, --num_rounds NUM_ROUNDS
                        Number of mixing rounds to do (default=2
  -u RPC_ADDRESS, --rpc_address RPC_ADDRESS
                        RPC address (default: from Rai config)
  -p RPC_PORT, --rpc_port RPC_PORT
                        RPC port (default: from Rai config)
  -g, --gui             Start the GUI (needs PyQt5 correctly installed)
```

## Roadmap

- Windows portable .exe file.
- Testing framework emulating the node.
- Better documentation.
- Progress bars both in text and GUI mode.

If you want to contribute a PR for any of these points you're more than welcome!

## I want to thank you!

xrb_3usnd3kirzfudprd3tceauh3sejxpfm754jgnjajbttrefx9obgdqe69wfcf (Original Maker's address)
