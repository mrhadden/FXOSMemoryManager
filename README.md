# FXOSMemoryManager
**Note: Reference Source Only**

## This code was adapted to save time from an embedded library called UMM.  I kept the source names the same to indicate the original source.  That said, modification have been made to that to suit the needs of the 65816 and FXOS.  It does a lot of bookkeeping that was easier to just use as a starting place, rather than write from scratch and have to debug everything.

*This source is provided to demostrate the memeory management system.  It will not compile, as is, but the functionality can be inspected.  One could remove the OS specific functionality and use the allocation/deallocation functionality.*




***Below ae the main memory manager methods.  The names are generally self explanatory.  Code comments will be added to each at a later date, as the functionlity is made permanent.*** 

```
#ifndef __FX_MEMORY_MANAGER
#define __FX_MEMORY_MANAGER

#include "fxos.h"
#include "umm_malloc_cfg.h"
#include "umm_malloc.h"

typedef struct _fx_memory_map
{
	ULONG availableMemory;
	UCHAR valid_segments[0xFF];
} FXMEMORYMAP;
typedef FXMEMORYMAP FAR *PFXMEMORYMAP;

typedef void (*MemoryCallback)(UCHAR seg,ULONG block, ULONG size);

ULONG k_heap_integrity_check(void);


LPVOID k_mem_object_copy(LPVOID object,UINT size);

ULONG k_check_system_memory(MemoryCallback callback);
ULONG k_fcheck_system_memory(MemoryCallback callback);

HANDLE k_mem_allocate_dynamic_heap(UINT size);
VOID k_mem_deallocate_dynamic_heap(HANDLE handle);

LPVOID k_mem_lock_handle(HANDLE handle);
VOID k_mem_unlock_handle(HANDLE handle);

LPVOID k_mem_allocate_heap(UINT size);
VOID k_mem_deallocate_heap(LPVOID);

LPVOID k_calloc(UINT n, UINT s);
void k_free(LPVOID ptr);

/*
 *  Handle OMF load segments
 * 	NOT IMPLEMENTED
 *
 */
HANDLE k_mem_load_segment(HANDLE handle);
LPVOID k_mem_lock_segment(HANDLE handle);
BOOL   k_mem_unlock_segment(HANDLE handle);
VOID   k_mem_unload_segment(LPVOID segment);

LPVOID k_mem_get_segment_info(HANDLE handle);


#endif
```

*More to come..*
