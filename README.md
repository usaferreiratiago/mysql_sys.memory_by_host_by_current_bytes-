# mysql_sys.memory_by_host_by_current_bytes-

SELECT 
    IF((`performance_schema`.`memory_summary_by_host_by_event_name`.`HOST` IS NULL),
        'background',
        `performance_schema`.`memory_summary_by_host_by_event_name`.`HOST`) AS `host`,
    SUM(`performance_schema`.`memory_summary_by_host_by_event_name`.`CURRENT_COUNT_USED`) AS `current_count_used`,
    FORMAT_BYTES(SUM(`performance_schema`.`memory_summary_by_host_by_event_name`.`CURRENT_NUMBER_OF_BYTES_USED`)) AS `current_allocated`,
    FORMAT_BYTES(IFNULL((SUM(`performance_schema`.`memory_summary_by_host_by_event_name`.`CURRENT_NUMBER_OF_BYTES_USED`) / NULLIF(SUM(`performance_schema`.`memory_summary_by_host_by_event_name`.`CURRENT_COUNT_USED`),
                            0)),
                    0)) AS `current_avg_alloc`,
    FORMAT_BYTES(MAX(`performance_schema`.`memory_summary_by_host_by_event_name`.`CURRENT_NUMBER_OF_BYTES_USED`)) AS `current_max_alloc`,
    FORMAT_BYTES(SUM(`performance_schema`.`memory_summary_by_host_by_event_name`.`SUM_NUMBER_OF_BYTES_ALLOC`)) AS `total_allocated`
FROM
    `performance_schema`.`memory_summary_by_host_by_event_name`
GROUP BY IF((`performance_schema`.`memory_summary_by_host_by_event_name`.`HOST` IS NULL),
    'background',
    `performance_schema`.`memory_summary_by_host_by_event_name`.`HOST`)
ORDER BY SUM(`performance_schema`.`memory_summary_by_host_by_event_name`.`CURRENT_NUMBER_OF_BYTES_USED`) DESC
