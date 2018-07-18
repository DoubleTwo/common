根据sql生成用户群脚本

1.执行方式

创建脚本 `getSegmenter.sh`
	   
```shell
	sql="
	select distinct (distinct_id) as distinct_id,
		   user_id,
		   1 as segment
	 from  events
	where  event = 'a0054f7bc3db02df' and
	       date between '2014-03-01' and '2018-03-31' and
	       $device_id in 
	       (select $device_id
		     from events
		    where event = 'a0054f7bc3db02df' and
		          date between '2014-03-01' and '2018-03-31'
		          group by $device_id having count(distinct user_id)<3)"
	echo $sql >segmenter.sql
	python3 custom_user_segmenter.py -w "https://test-hechun.cloud.sensorsdata.cn/api" \
			-l "http://test-hechun.cloud.sensorsdata.cn:8006/sa?project=gaoyujie&token=d28b875ed9ac268f" \
			-t 1593f4465182b53237d71b370333bb1c47f01796270effb47723d70c06cad40c \
			-d tmp -f segmenter.sql -n 'testsegmenter'
```
创建临时文件夹 -- -d 后面的文件夹名字
	   

	mkdir tmp

	   
执行脚本 `getSegmenter.sh`
	   

    sh getSegmenter.sh

2.输入参数说明
	

	  -w API_URL, --api_url API_URL
	                        Sensors Analytics api url
      -t API_TOKEN, --api_token API_TOKEN
	                        Sensors Analytics api token ( API Secret )
	  -l LOGGING_URL, --logging_url LOGGING_URL
	                        Sensors Analytics logging url
	  -d TEMP_DIR, --temp_dir TEMP_DIR
	                        temporary directory
	  -f SQL_FILE, --sql_file SQL_FILE
	                        segmenter SQL in a file
	  -n PROFILE_NAME, --profile_name PROFILE_NAME
	                        segmenter profile name

3.输出文件

`tmp_dir` 下的文件为即将修改属性值的所有用户
  
4.注意事项

建议在内网环境下使用该程序，因为会出现超时和并法数不足问题