![[Pasted image 20250815012609.png]]![[Pasted image 20250815012743.png]]
```java
{
  "query_block": {
    "select_id": 1,
    "cost_info": {
      "query_cost": "623.62"
    },
    "nested_loop": [
      {
        "table": {
          "table_name": "wi",
          "access_type": "ALL",
          "rows_examined_per_scan": 162,
          "rows_produced_per_join": 162,
          "filtered": "100.00",
          "cost_info": {
            "read_cost": "18.10",
            "eval_cost": "32.40",
            "prefix_cost": "50.50",
            "data_read_per_join": "87K"
          },
          "used_columns": [
            "id",
            "waybill_code",
            "poi_id",
            "poi_name",
            "delivery_tag",
            "waybill_create_time"
          ],
          "materialized_from_subquery": {
            "using_temporary_table": true,
            "dependent": false,
            "cacheable": true,
            "query_block": {
              "select_id": 2,
              "cost_info": {
                "query_cost": "45531.44"
              },
              "ordering_operation": {
                "using_filesort": true,
                "cost_info": {
                  "sort_cost": "162.03"
                },
                "table": {
                  "table_name": "waybill_info",
                  "access_type": "range",
                  "possible_keys": [
                    "PRIMARY",
                    "idx_waybill_create_time",
                    "idx_poi_id_waybill_create_time"
                  ],
                  "key": "idx_poi_id_waybill_create_time",
                  "used_key_parts": [
                    "poi_id",
                    "waybill_create_time"
                  ],
                  "key_length": "21",
                  "rows_examined_per_scan": 32406,
                  "rows_produced_per_join": 162,
                  "filtered": "0.50",
                  "index_condition": "((`mall_tms_monitor`.`waybill_info`.`poi_id` = 10018) and (`mall_tms_monitor`.`waybill_info`.`waybill_create_time` >= '2025-06-01 00:00:00') and (`mall_tms_monitor`.`waybill_info`.`waybill_create_time` <= '2025-07-31 00:00:00') and (`mall_tms_monitor`.`waybill_info`.`id` > 0))",
                  "cost_info": {
                    "read_cost": "45337.00",
                    "eval_cost": "32.41",
                    "prefix_cost": "45369.41",
                    "data_read_per_join": "577K"
                  },
                  "used_columns": [
                    "id",
                    "waybill_code",
                    "poi_id",
                    "poi_name",
                    "status",
                    "waybill_create_time",
                    "delivery_tag",
                    "valid"
                  ],
                  "attached_condition": "((`mall_tms_monitor`.`waybill_info`.`valid` = 1) and (`mall_tms_monitor`.`waybill_info`.`status` = 60))"
                }
              }
            }
          }
        }
      },
      {
        "table": {
          "table_name": "waybill_poi_info",
          "access_type": "ref",
          "possible_keys": [
            "uniq_waybill_code_identify_code",
            "idx_waybill_ship_time"
          ],
          "key": "idx_waybill_ship_time",
          "used_key_parts": [
            "waybill_code"
          ],
          "key_length": "258",
          "ref": [
            "wi.waybill_code"
          ],
          "rows_examined_per_scan": 2,
          "rows_produced_per_join": 47,
          "filtered": "10.00",
          "cost_info": {
            "read_cost": "477.60",
            "eval_cost": "9.55",
            "prefix_cost": "623.62",
            "data_read_per_join": "125K"
          },
          "used_columns": [
            "waybill_code",
            "identify_code",
            "address_code",
            "address_name",
            "poi_type",
            "sort",
            "fence_enter_time",
            "fence_leave_time",
            "sign_in_time",
            "ship_or_sign_time",
            "valid"
          ],
          "attached_condition": "(`mall_tms_monitor`.`waybill_poi_info`.`valid` = 1)"
        }
      }
    ]
  }
}
```
**走索引下推：**
```java

{
    "query_block": {
        "select_id": 1,
        "cost_info": {
            "query_cost": "623.62"
        },
        "nested_loop": [
            {
                "table": {
                    "table_name": "wi",
                    "access_type": "ALL",
                    "rows_examined_per_scan": 162,
                    "rows_produced_per_join": 162,
                    "filtered": "100.00",
                    "cost_info": {
                        "read_cost": "18.10",
                        "eval_cost": "32.40",
                        "prefix_cost": "50.50",
                        "data_read_per_join": "87K"
                    },
                    "used_columns": [
                        "id",
                        "waybill_code",
                        "poi_id",
                        "poi_name",
                        "delivery_tag",
                        "waybill_create_time"
                    ],
                    "materialized_from_subquery": {
                        "using_temporary_table": true,
                        "dependent": false,
                        "cacheable": true,
                        "query_block": {
                            "select_id": 2,
                            "cost_info": {
                                "query_cost": "45369.41"
                            },
                            "ordering_operation": {
                                "using_filesort": false,
                                "table": {
                                    "table_name": "waybill_info",
                                    "access_type": "range",
                                    "possible_keys": [
                                        "PRIMARY",
                                        "idx_waybill_create_time",
                                        "idx_poi_id_waybill_create_time"
                                    ],
                                    "key": "PRIMARY",
                                    "used_key_parts": [
                                        "id"
                                    ],
                                    "key_length": "8",
                                    "rows_examined_per_scan": 1033251,
                                    "rows_produced_per_join": 162,
                                    "filtered": "0.02",
                                    "cost_info": {
                                        "read_cost": "45337.00",
                                        "eval_cost": "32.41",
                                        "prefix_cost": "45369.41",
                                        "data_read_per_join": "577K"
                                    },
                                    "used_columns": [
                                        "id",
                                        "waybill_code",
                                        "poi_id",
                                        "poi_name",
                                        "status",
                                        "waybill_create_time",
                                        "delivery_tag",
                                        "valid"
                                    ],
                                    "attached_condition": "((`mall_tms_monitor`.`waybill_info`.`valid` = 1) and (`mall_tms_monitor`.`waybill_info`.`status` = 60) and (`mall_tms_monitor`.`waybill_info`.`poi_id` = 10018) and (`mall_tms_monitor`.`waybill_info`.`waybill_create_time` >= '2025-06-01 00:00:00') and (`mall_tms_monitor`.`waybill_info`.`waybill_create_time` <= '2025-07-31 00:00:00') and (`mall_tms_monitor`.`waybill_info`.`id` > 0))"
                                }
                            }
                        }
                    }
                }
            },
            {
                "table": {
                    "table_name": "waybill_poi_info",
                    "access_type": "ref",
                    "possible_keys": [
                        "uniq_waybill_code_identify_code",
                        "idx_waybill_ship_time"
                    ],
                    "key": "idx_waybill_ship_time",
                    "used_key_parts": [
                        "waybill_code"
                    ],
                    "key_length": "258",
                    "ref": [
                        "wi.waybill_code"
                    ],
                    "rows_examined_per_scan": 2,
                    "rows_produced_per_join": 47,
                    "filtered": "10.00",
                    "cost_info": {
                        "read_cost": "477.60",
                        "eval_cost": "9.55",
                        "prefix_cost": "623.62",
                        "data_read_per_join": "125K"
                    },
                    "used_columns": [
                        "waybill_code",
                        "identify_code",
                        "address_code",
                        "address_name",
                        "poi_type",
                        "sort",
                        "fence_enter_time",
                        "fence_leave_time",
                        "sign_in_time",
                        "ship_or_sign_time",
                        "valid"
                    ],
                    "attached_condition": "(`mall_tms_monitor`.`waybill_poi_info`.`valid` = 1)"
                }
            }
        ]
    }
}
```