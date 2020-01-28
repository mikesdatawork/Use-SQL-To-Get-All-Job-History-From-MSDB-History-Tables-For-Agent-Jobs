![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL To Get All Job History From MSDB History Tables For Agent Jobs
**Post Date: June 13, 2016**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's some quick SQL Logic which basically shows you the job history, with step history and step durations.</p>    


## SQL-Logic
```SQL
use msdb;
set nocount on
 
select
    'job_name'      =   sj.name
-- when step_id is zero '0', then you are seeing the final report of the job status.
,   'step_id'       =   sjh.step_id
,   'step_name'     =   case sjh.step_name
                            when '(Job outcome)' then 'Job Completed'
                            else sjh.step_name
                        end
,   'status'        =   case
                            when sjh.run_status = 0 then 'Failed'
                            when sjh.run_status = 1 then 'Succeeded'
                            when sjh.run_status = 2 then 'Retry'
                            when sjh.run_status = 3 then 'Canceled'
                            when sjh.run_status = 4 then 'In-Progress'
                            when sjh.run_status = 5 then 'unknown'
                        end
,   'start_time'    =   left(msdb.dbo.agent_datetime(sjh.run_date, sjh.run_time), 19)
-- duration is a conversion of seconds to: Days, Hours, Minutes, Seconds                
,   'duration'      = 
                        right('0' + cast(sjh.run_duration   /60/60/24%30    as varchar),2)  + ':' +
                        right('0' + cast(sjh.run_duration   / 3600          as varchar),2)  + ':' +
                        right('0' + cast((sjh.run_duration  / 60) % 60      as varchar),2)  + ':' +
                        right('0' + cast(sjh.run_duration   % 60            as varchar),2)
from       
    sysjobs sj inner join  sysjobhistory sjh on sjh.job_id = sj.job_id
order by
    sjh.instance_id, sjh.run_date, sjh.run_time asc
```


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

     
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

