## Build Cube

After cube is created, it has to be built, and then queries can execute. We will use KAP sample data to show the process of cube building.

#### First build

Open KAP web UI, select "Kylin_Sample" project, go to the "Model" page, and find the cube list.
Step one, find the "Kylin_Sample_Cube_1" cube, right click the "Action" button, and select "Build" in the dropdown menu.

![](/images/molap/buildcube_0.png)

Step two, in the build cube dialog, confirm the Partition Date Column is DEFAULT.KYLIN_SALES.PART_DT, and its start time is 2012-01-01 00:00:00. In KAP, every build will create a new segment in cube, and all segments will participate in queries. Bigger segments are faster in query but takes longer to build and refresh. Smaller segments are easier to build and refresh but have a small penalty to query performance. In this case, we choose the end date of segment to be 2013-01-01 00:00:00. Click "Submit" to continue.

> Note: User shall manage the granularity of segments. The time range of segments may depend on data volume, ETL data arrival time, and other business need. Typically new segments are kept small to allow quicker build and easier refresh. Later, as data ages, historical segments are merged into bigger ones, to control the overall number of segments. In this example, since the volume is small, the one-year segment can be built without any problem.

![](/images/molap/buildcube_1.png)

After submission, go to the "Monitor" page, a list of running jobs will appear. The first job (named "Kylin_Sample_Cube_1 - 20120101000000_20130101000000") is the job we just submitted. Double click the job (or single click the arrow icon on the right), the detail steps of the job will expand. When all steps complete, the status of the job will become "Finished", and the first segment is built. Go to the cube list, check the cube status is now "Ready".

#### Incremental Build

After the first segment is built, we can build more segments incrementally to accommodate newly arrived data. First find the cube in the "Model" page, click the "Action" button and select "Build" in the dropdown menu. In the build dialog, confirm the start time is 2013-01-01 00:00:00. This is where the last segment ends. To ensure continuity, a new segment always starts from the end of the last segment. Enter 2014-01-01 00:00:00 as the end of the new segment and submit the job.

![](/images/molap/buildcube_2.png)

When build completes, go to cube details page and check, there shall be two segments under the cube.

![](/images/molap/buildcube_3.png)
