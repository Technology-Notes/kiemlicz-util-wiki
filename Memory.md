#RAM

`free (-m to show in MB)`
 - total: indicates memory/physical RAM available for your machine. By default these numbers are in KB's.
 - used: indicates memory/RAM used by system. This includes buffers and cached data size as well.
 - free: indicates total **unused** RAM available for new process to run.
 - shared:  indicates shared memory. This column is obsolete and may be removed in future releases of free.
 - buffers: indicates total RAM buffered by different applications in Linux
 - cached: indicates total RAM used for Caching of data for future purpose
 - -/+ buffers/cache: shows _used_ column minus (_buffers_+_cached_) and _free_ column plus (_buffers_+_cached_). 
 Why is that? Because when memory used is getting up to limit, the buffers + cache will be freed and used by demanding applications. 
 This show most accurate memory usage.
 
New version of `free`:
 - buff/cache: sum of buffers and cached
 - available: not exactly _free_ column plus (_buffers_+_cached_)