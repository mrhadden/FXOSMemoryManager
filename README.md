# FXOSMemoryManager
**Note: Reference Source Only**

## This code was adapted to save time from an embedded library called UMM.  I kept the source names the same to indicate the original source.  That said, modification have been made to that to suit the needs of the 65816 and FXOS.

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

*Examples of Use*

## Allocate a list
```
_k_windowManager_WindowClassList 	= k_nodelist_allocate_list("_window_class_list" ,k_deallocate_window_class);
```

*Deallocation Function*
```
VOID k_deallocate_window_class(LPCSTR name,LPVOID data)
{
	PWINDOW pWin = NULL;
	k_debug_strings("k_deallocate_window_class name:",(LPSTR)name);
	k_debug_pointer("                            ptr:",data);

	k_mem_deallocate_heap(data);
}
```

## Deallocate a list
```
VOID k_deallocate_window_object(LPCSTR name,LPVOID data)
{
	PWINDOW pWin = NULL;
	k_debug_strings("k_deallocate_window_object name:",(LPSTR)name);
	k_debug_pointer("                            ptr:",data);

	if(data)
	{
		pWin = (PWINDOW)data;
		k_debug_strings("                        caption:",(LPSTR)pWin->win_title);


		if(pWin->pChildHitList)
		{
			k_nodelist_deallocate_list(pWin->pChildHitList);
		}
		if(pWin->pChildWindows)
		{
			k_nodelist_deallocate_list(pWin->pChildWindows);
		}
		if(pWin->windowData)
			k_mem_deallocate_heap(pWin->windowData);


		k_mem_deallocate_heap(pWin);
	}

}
```

## FOREACH Functionality
*Iterate a nodelist and apply a data check callback for a condition (TRUE/FALSE)*
```
HWND k_user_findWindow(LPCSTR winTitle)
{
	PFXNODE node = NULL;
	HWND hWnd = NULL;

	node = k_nodelist_foreach_until_listdata(_k_windowManager_WindowObjectList,(LPVOID)winTitle,find_window_caption);
	if(node)
	{
		hWnd = k_getHandleFromWindow((PWINDOW)node->data);
	}
	return hWnd;

}
```

*FOREACH Callback*
```
BOOL find_window_caption(LPVOID ctx,LPVOID data)
{
	BOOL bRet = FALSE;

	if(ctx && data)
	{
		if(strcmp((LPCHAR)ctx,((PWINDOW)data)->win_title) == 0)
			bRet = TRUE;
	}

	return bRet;
}
```
