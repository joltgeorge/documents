# Requirements

## 1. Pruning Settings

In order to run a full node, different hardware requirements should be met based on the pruning strategy you would like to use.

_Pruning_ is the term used to identify the periodic action that can be taken in order to free some disk space on your full node. This is done by removing old blocks data from the disk, freeing up space.

Inside the Joltify Network, there are various types of pruning strategies that can be applied. The main ones are:

* `default`: the last 100 states are kept in addition to every 500th state; pruning at 10 block intervals
* `nothing`: all historic states will be saved, nothing will be deleted (i.e. archiving node)
* `everything`: all saved states will be deleted, storing only the current state; pruning at 10 block intervals (At the moment this option is not recommended as it can easily corrupt the database and the node will halt)
* `custom`: allow pruning options to be manually specified through 'pruning-keep-recent', 'pruning-keep-every', and 'pruning-interval'

## 2. Hardware Requirements

You can easily understand how using a pruning strategy of `nothing` will use more disk space than `everything`. For this reason, there are different disk space that we recommend based on the pruning strategy you choose:

| Pruning strategy | Minimum disk space | Recommended disk space |
| :--------------: | :----------------: | :--------------------: |
|   `everything`   |        20GB        |          60GB          |
|     `default`    |        80GB        |          160GB         |
|     `nothing`    |        120GB       |         >300GB         |

Apart from disk space, the following requirements should be met:

<table><thead><tr><th align="center">Minimum CPU cores</th><th align="center">Recommended CPU cores</th><th data-hidden></th></tr></thead><tbody><tr><td align="center">4</td><td align="center">8</td><td></td></tr></tbody></table>

| Minimum RAM | Recommended RAM |
| :---------: | :-------------: |
|     8GB     |       16GB      |







