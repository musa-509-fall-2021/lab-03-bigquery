# Working with BigQuery

**What We'll do today:**
1. Explain another PostgreSQL join type (for assignment #2)
2. Add new tables to BigQuery
3. Query BigQuery data and visualize it with Data Studio and GeoViz

## Adding Tables to BigQuery

There are a few ways to add data to BigQuery. Today we're going to go through the interface.

1. Create a project in GCP

2. From the BigQuery interface, the dots next to your project and select **Create Dataset**; a dataset is a grouping of tables; name the dataset according to your domain (mine is _city_of_phl_)

3. Open your dataset (using the three dots next to the name) and click the **Create Table** button
  * For **Source**, choose _Google Cloud Storage_
  * For **File Format**, choose _CSV_
  * For the **GCS Bucket**, enter `gs://mjumbewu_musa_509/phl_dor/phl_dor_retransfers_*`
  * Give your table a name (I chose `dor_retransfers` -- i.e. **r**eal **e**state **transfers**)
  * Select **Auto detect Schema and input parameters****
  * Expand the **Advanced options** and select the **Allow quoted newlines** checkbox
  * Leave everything else as its default and click **Create**

4. Repeat the process from #3 for the GCS sources `gs://mjumbewu_musa_509/phl_li/phl_li_permits_*` (table name `li_permits`) and `gs://mjumbewu_musa_509/phl_li/phl_li_violations_*` (table name `li_violations`).

5. Note that each table has a field called `the_geom_wkt`, and that it has a type `string`. Add a column that has a type `geography` where we will load the actual geo values. I.e., repeat this query for each table:

  ```sql
  alter table city_of_phl.dor_retransfers
      add column the_geog geography;

  update city_of_phl.dor_retransfers
      set the_geog = ST_GeogFromText(the_geom_wkt)
      where true;
  ```

  The `where true` in this case just means do this on every row. Why did I include semi-colons (`;`) in the above code?

## Run a few queries against public data

1. Pin the `bigquery-public-data` project into your own project.
2. Using the `bigquery-public-data.geo_census_blockgroups.blockgroups_42` table, map the number of real estate transfers in each Philadelphia census tract (`geo_id` starts with `42101`) in 2020.
2. Using the `bigquery-public-data.geo_census_blockgroups.blockgroups_42` table, chart the number of real estate transfers each month since the year 2000.
