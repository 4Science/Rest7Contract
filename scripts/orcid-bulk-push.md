# ORCID-Bulk-Push script
<!-- TOC -->
* [ORCID-Bulk-Push script](#orcid-bulk-push-script)
  * [What it does?](#what-it-does)
  * [When we use it?](#when-we-use-it)
  * [How it Works?](#how-it-works)
  * [Parameters](#parameters)
  * [Conclusion/QuickGuide](#conclusionquickguide)
<!-- TOC -->
## What it does?

The ORCID-Bulk-Push script is used to perform a bulk synchronization of all the ORCID entities that are placed in the
ORCID queue. This script is specifically designed to work with ORCID integration in DSpace.

## When we use it?

This script can be useful if you want to ensure that all the entities in the queue are synchronized, even if there have
been synchronization failures in the past.

## How it Works?

The ORCID-Bulk-Push script works by querying the DSpace database to identify all the ORCID entities that are placed in
the ORCID queue. It then attempts to synchronize these entities with ORCID. If an entity fails to synchronize, the
script will retry the synchronization up to a maximum number of attempts. By default, the maximum number of attempts is
set to 10. However, this can be changed in the ORCID configuration file.

If the `-f` option is used, the script will force the synchronization, even if the maximum attempts have been reached.

## Parameters

The following parameters are available for the ORCID-Bulk-Push script:

| Parameter            | Description                                               |
|----------------------|-----------------------------------------------------------|
| `-f`, <br/>`--force` | Force the synchronization, ignoring the maximum attempts. |

## Conclusion/QuickGuide

In conclusion, the ORCID-Bulk-Push script is a useful tool for performing a bulk synchronization of all the ORCID
entities in the queue. By synchronizing these entities, you can ensure that the data in DSpace is up-to-date with ORCID.
If you encounter synchronization failures, the script will automatically retry the synchronization up to a maximum
number of attempts. If you want to ensure that all the entities in the queue are synchronized, even if there have been
synchronization failures in the past, you can use the `-f` option to force the synchronization.
