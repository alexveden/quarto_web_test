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
test$case(os_cmd_join_timeout)
{
    os_cmd_c c = { 0 };
    // WTF: if we call subprocess_terminate() on empty struct it will kill self!
    tassert_eq(0, os.cmd.is_alive(&c));
    e$ret(os.cmd.kill(&c));

    mem$scope(tmem$, _)
    {
        arr$(char*) args = arr$new(args, _);
        arr$pushm(args, test_app("sleep", _), "2", NULL);
        tassert_er(EOK, os.cmd.create(&c, args, arr$len(args), NULL));
        tassert_eq(1, os.cmd.is_alive(&c));

        int err_code = 0;
        tassert_er(Error.timeout, os.cmd.join(&c, 1, &err_code));
        tassert_eq(err_code, -1);

        tassert_er(EOK, os.cmd.create(&c, args, arr$len(args), NULL));

        err_code = 777;
        tassert_er(Error.ok, os.cmd.join(&c, 3, &err_code));
        tassert_eq(err_code, 0);
    }
    return EOK;
}
```
