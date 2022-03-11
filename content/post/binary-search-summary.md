---
title: "二分查找总结"
_build:
  render: never
  list: never
  publishResources: false
---

# 二分查找总结

当区间 `[l, r]` 的更新操作是 `r = mid; l = mid + 1;` 时，计算 `mid` 不需要加 `1`

```c++
int bsearch_1(int l, int r) {
    while (l < r) {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;
        else l = mid + 1;
    }
    return l;
}
```
当区间 `[l, r]` 的更新操作是 `r = mid - 1; l = mid;` 时，计算 `mid` 需要加 `1`

```c++
int bsearch_2(int l, int r) {
    while (l < r) {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```