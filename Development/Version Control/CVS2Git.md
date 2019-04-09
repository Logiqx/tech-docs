# Migration from CVS to Git

## Background

I migrated three of my CVS repositories to Git and the results can now be found on GitHub:

* Logiqx.com - [Tools](https://github.com/Logiqx/logiqx-dev)
* Logiqx.com - [WWW](https://github.com/Logiqx/logiqx-www)
* [CAESAR](https://github.com/Logiqx/caesar/)



I documented the migration activities for all three repositories:

- Migration of Logiqx.com - [Tools](https://github.com/Logiqx/logiqx-dev/blob/master/Migration.md)
- Migration of Logiqx.com - [WWW](https://github.com/Logiqx/logiqx-www/blob/master/Migration.md)
- Migration of [CAESAR](https://github.com/Logiqx/caesar/blob/master/Migration.md)



It should be noted that CVS tracks the history of individual files unlike the multiple file change sets of Git.

I used tags to record the file revisions within each "release" in CVS which were converted to branches in Git.

The branches are "stale" and an artifact of cvs-fast-export but they can still be used as historical snapshots.

