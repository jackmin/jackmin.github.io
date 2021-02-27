#Shared RSS:

1. `__flow_dv_action_rss_update()` only update queue number and doesn't return error if update other fileds
    If queue are not changed, it'll update anyway.
2. `__flow_dv_action_rss_hrxq_lookup()` for shared rss hrxq lookup.
    It only compare `hash_field`.
    But, legacy `mlx5_hrxq_get()` lookup by using key, keylen, and hash_field.
3. hrxq = tir, `mlx5_devx_tir_attr_set()` -> always set hash_func to
