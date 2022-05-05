# 1. Forecast Batching simplified

5.5.2022

## Status (Proposed, Accepted, Rejected)

Proposed  

## Context

There are a number of ways for naturally batching forecasts like by location, forecast type and missing only. Currently we are supporting different types of batches for these different type making the logic in the forecast service (FS) cumbersome. With the new Forecast Batch service (FBS) & Kubernetes we have the ability to parallel process forecasting batches and these "natural" batches do not lend them selves very well to that parallelism since each batch might be quite large so no option to actually batch them.

The proposal is to instead extract some of the logic that now resides in the FS into the FBS and have the FBS create Rabbit MQ messages that are completely uniform regardless of the actual "natural" batch chosen in the beginning of the process. The Rabbit MQ message would contain a list of all the items that should be processed in that batch and the FS only needs to do the forecast for each item in batch and not bother about any checks and/or data cleansing before starting. The FBS would use a default batch size of e.g. 1000 items so when forecasting for a location that contains 10.000 items they would be split automatically into 10 batches and process in parallel.

There are multiple upsides to this approach:
 - Seperation of concers (FS becomes much leaner and simpler)
 - Any form of "natural" batching will lend itself to parallelism
 - Having the actual list of items can make it easer to re-do a batch if a single items fails in the process
 - All batches are handled in the same manner in the FS and will make it much easier to maintain and improve

 Downside:
 - The Rabbit MQ message will be a larger but should only be about 0.1 MB where the recomendation from Rabbit is to keep message smaller then 128 MB and the theoratical max size is 2 GB
## Decision



## Consequences


