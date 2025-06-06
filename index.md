---
title: "."
---

This is a Quarto website. [about](about.md)

To learn more about Quarto websites visit <https://quarto.org/docs/websites>.

::: {#tip-example .callout-tip}
## Cross-Referencing a Tip

Add an ID starting with `#tip-` to reference a tip.
:::

{{< include _index.html >}}

> [!NOTE]
> Useful information that users should know, even when skimming content.

> [!TIP]
> Helpful advice for doing things better or more easily.

> [!IMPORTANT]
> Key information users need to know to achieve their goal.

> [!WARNING]
>
> Urgent info that needs immediate user attention to avoid problems.
> Some text

> [!CAUTION]
> Advises about risks or negative outcomes of certain actions.

```c
// CEX has special exception return type that forces the caller to check return type of calling
//   function, also it provides support of call stack printing on errors in vanilla C
Exception
cmd_custom_test(u32 argc, char** argv, void* user_ctx)
{
    // Let's open temporary memory allocator scope (var name is `_`)
    //  it will free all allocated memory after any exit from scope (including return or goto)
    mem$scope(tmem$, _)
    { 
        e$ret(os.fs.mkpath("tests/build/")); // make directory or return error with traceback
        e$assert(os.path.exists("tests/build/")); // evergreen assertion or error with traceback

        // auto type variables
        var search_pattern = "tests/os_test/*.c";

        // Trace with file:<line> + formatting
        log$trace("Finding/building simple os apps in %s\n", search_pattern);

        // Search all files in the directory by wildcard pattern
        //   allocate the results (strings) on temp allocator arena `_`
        //   return dynamic array items type of `char*`
        arr$(char*) test_app_src = os.fs.find(search_pattern, false, _);

        // for$each works on dynamic, static arrays, and pointer+length
        for$each(src, test_app_src)
        {
            char* tgt_ext = NULL;
            char* test_launcher[] = { cexy$debug_cmd }; // CEX macros contain $ in their names

            // arr$len() - universal array length getter 
            //  it supports dynamic CEX arrays and static C arrays (i.e. sizeof(arr)/sizeof(arr[0]))
            if (arr$len(test_launcher) > 0 && str.eq(test_launcher[0], "wine")) {
                // str.fmt() - using allocator to sprintf() format and return new char*
                tgt_ext = str.fmt(_, ".%s", "win");
            } else {
                tgt_ext = str.fmt(_, ".%s", os.platform.to_str(os.platform.current()));
            }

            // NOTE: cexy is a build system for CEX, it contains utilities for building code
            // cexy.target_make() - makes target executable name based on source
            char* target = cexy.target_make(src, cexy$build_dir, tgt_ext, _);

            // cexy.src_include_changed - parses `src` .c/.h file, finds #include "some.h",
            //   and checks also if "some.h" is modified
            if (!cexy.src_include_changed(target, src, NULL)) {
                continue; // target is actual, source is not modified
            }

            // Launch OS command and get interactive shell
            // os.cmd. provides more capabilities for launching subprocesses and grabbing stdout
            e$ret(os$cmd(cexy$cc, "-g", "-Wall", "-Wextra", "-o", target, src));
        }
    }

    // CEX provides capabilities for generating namespaces (for user's code too!)
    // For example, cexy namespace contains
    // cexy.src_changed() - 1st level function
    // cexy.app.run() - sub-level function
    // cexy.cmd.help() - sub-level function
    // cexy.test.create() - sub-level function
    return cexy.cmd.simple_test(argc, argv, user_ctx);
}
```
