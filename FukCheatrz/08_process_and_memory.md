# 08 - Process and Memory Manipulation

## ntdll Patching

Extracted from app.so:

```
_keepPatchedNtDllBytes
_writeNewNtdllBytes
_protectMemoryRegionAndWriteBufferIntoIt
```

These three function names describe a complete in-memory ntdll patching operation. ntdll.dll is the lowest-level user-space Windows system library, the boundary between user-mode code and the Windows kernel. Antivirus software, EDR products, and anti-cheat systems commonly install hooks into ntdll functions to monitor API calls made by running processes.

_keepPatchedNtDllBytes indicates the application stores a copy of its modified ntdll bytes to maintain the patch across any restoration attempts. _writeNewNtdllBytes writes the modified bytes into the ntdll mapping in the process's virtual address space. _protectMemoryRegionAndWriteBufferIntoIt handles the memory protection state change required before writing into a read-only mapped region.

The practical effect is that after this operation runs, any hooks previously installed into ntdll by security software in that process are overwritten and cease to function. The security product continues to believe it is monitoring the process, but its visibility into subsequent API calls is removed.

## DLL Injection Methods

Extracted from app.so:

```
get:injectionMethod
_selfLoadLibraryInGameProcess
_insertImportFunctions
_relocateImage
```

get:injectionMethod is a getter from the application's state model, indicating injection method is a user-selectable option exposed in the UI (confirmed by injection.png in the asset manifest).

_selfLoadLibraryInGameProcess describes a standard LoadLibrary-based injection approach, where the application causes the target process to load a DLL from disk by calling LoadLibrary in the target process's context.

_insertImportFunctions and _relocateImage together describe manual map injection. Manual mapping involves loading a DLL image directly into the target process's memory without using the Windows loader, by manually parsing the PE header, allocating memory, copying sections, applying base relocations, and resolving imports. Because the DLL is never registered with the Windows loader, it does not appear in the target process's module list, making it invisible to tools that enumerate loaded modules.

The failure string extracted from app.so confirms this is live code:

```
Steam module manual map injection has failed! Code:
```

This is a user-facing error message with a trailing code placeholder, indicating the application reports injection failures to the user with a numeric error code.

## Unity and Mono Runtime Injection

Extracted from app.so:

```
_openMonoAssembly
_invokeMonoLibraryRoutines
```

These function names describe injection targeting the Mono runtime, which is the scripting engine used by Unity-based games. Rather than injecting a native DLL, this method opens the game's Mono scripting environment and invokes managed code within it. This is a separate injection pathway from the native DLL injection methods above and specifically targets the large category of games built on the Unity engine.

## Target Process Location

The application locates target game processes using Steam's registry key:

```
HKEY_CURRENT_USER\Software\Valve\Steam\ActiveProcess
```

This key contains the PID of the running Steam client and the Steam installation path. From this the application can enumerate Steam game processes to identify injection targets without relying on hardcoded process names.
