
# Short guide to assign LSOA IMD to antennas based on their largest overlap with LSOA 


```python
import pandas as pd
# read telefonica antennas
telefonica_antenna_London_location = "C://#CVUT//work_current//#Telefonica_internship//qgis_project//layers//ArcGIS//telefonica_antenna_London.csv"
telefonica_antenna_London = pd.read_csv(telefonica_antenna_London_location)
# filter 3/4G antennas
telefonica_antenna_London = telefonica_antenna_London[(telefonica_antenna_London['generation']=='3G') | (telefonica_antenna_London['generation']=='4G')]

# assign unique group ID to groups of colocated antennas
# from https://www.reddit.com/r/learnpython/comments/69d34g/create_unique_id_for_each_group_in_pandas/
telefonica_antenna_London['Group_ID'] = telefonica_antenna_London.groupby(['easting','northing']).grouper.group_info[0]
telefonica_antenna_London.to_csv("C://#CVUT//work_current//#Telefonica_internship//qgis_project//layers//ArcGIS//telefonica_antenna_London_Group_ID.csv",index=False, columns=['easting','northing','Group_ID'])
```

1. download ARCgis Pro desktop and activate trial 21day version
2. insert LSOA dataset into project - only ESRI .shp files are supported so you most probably have to save LSOA dataset in QGIS as .shp in order to import it to QRCgis
3. insert telefonica_antenna_London_Group_ID containg Group_IDs to project, the dataset must be in same CRS (e.g. EPSG:4326) and it must be .shp (csv is not supported in ARCgis?) - so you most probably have to save it crs/shp in QGIS
4. generate Voronoi cells from antenna dataset - called Thiessen in ARCgis
5. import "Spatial Join - Largest Overlap" tool to toolbox : https://www.arcgis.com/home/item.html?id=e9cccd343bf84916bda1910c31e5eab2
6. apply it on Voronoi cells/LSOA
7. export the table to .xls (direct export to csv is not supported in ARCgis :D) - "Table to Excel" tool
8. filter out empty cells (voronoi cells outside the LSOAs) and convert the table to .csv
9. run rest of the commands below


```python
# read a new file and merge it with Telefonica London antenna dataset based on Group ID
telefonica_antenna_London_Group_ID_LSOA_IMD = pd.read_csv("C://#CVUT//work_current//#Telefonica_internship//qgis_project//layers//ArcGIS//London_Antenna_Group_ID_LSOA_IMD.csv")
telefonica_antenna_London_joined = pd.merge(telefonica_antenna_London, telefonica_antenna_London_Group_ID_LSOA_IMD,  how='inner', left_on=["Group_ID"], right_on = ["Group_ID"])
# rename columns to match previous dataset that is used in the scripts
telefonica_antenna_London_joined.rename(columns={'generation': 'gen', 'LSOA_IMD': 'LSOA_IMD_decile'}, inplace=True)
telefonica_antenna_London_joined.to_csv("C://#CVUT//work_current//#Telefonica_internship//qgis_project//layers//ArcGIS//LondonAntennaGenerationIMD_v2.csv",index=False, columns=['gen','lkey','LSOA_IMD_decile'])
```


```python
telefonica_antenna_London_joined
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>gen</th>
      <th>easting</th>
      <th>northing</th>
      <th>lkey</th>
      <th>Group_ID</th>
      <th>LSOA_IMD_decile</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-10759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-20759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-40759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-10759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-40759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-20759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>6</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-30759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>7</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-50759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>8</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-30759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>9</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-50759</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>10</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-15491</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>11</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-25491</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>12</th>
      <td>3G</td>
      <td>532718.0</td>
      <td>181075.0</td>
      <td>21032-35491</td>
      <td>2127</td>
      <td>7</td>
    </tr>
    <tr>
      <th>13</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-00699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>14</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-10699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>15</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-40699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>16</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-00699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>17</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-30699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>18</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-50699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>19</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-10699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>20</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-20699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>21</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-40699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>22</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-20699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>23</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-30699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>24</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-50699</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>25</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-25431</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>26</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-15431</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>27</th>
      <td>3G</td>
      <td>531511.0</td>
      <td>178243.0</td>
      <td>21008-35431</td>
      <td>1900</td>
      <td>3</td>
    </tr>
    <tr>
      <th>28</th>
      <td>3G</td>
      <td>529458.0</td>
      <td>176521.0</td>
      <td>21052-10947</td>
      <td>1519</td>
      <td>3</td>
    </tr>
    <tr>
      <th>29</th>
      <td>3G</td>
      <td>529458.0</td>
      <td>176521.0</td>
      <td>21052-20947</td>
      <td>1519</td>
      <td>3</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>24060</th>
      <td>3G</td>
      <td>529122.0</td>
      <td>178660.0</td>
      <td>21052-11151</td>
      <td>1453</td>
      <td>6</td>
    </tr>
    <tr>
      <th>24061</th>
      <td>4G</td>
      <td>539202.0</td>
      <td>180225.0</td>
      <td>110-500953</td>
      <td>2775</td>
      <td>2</td>
    </tr>
    <tr>
      <th>24062</th>
      <td>4G</td>
      <td>527585.0</td>
      <td>179495.0</td>
      <td>114-522696</td>
      <td>1242</td>
      <td>5</td>
    </tr>
    <tr>
      <th>24063</th>
      <td>4G</td>
      <td>543472.0</td>
      <td>179203.0</td>
      <td>110-100417</td>
      <td>2963</td>
      <td>6</td>
    </tr>
    <tr>
      <th>24064</th>
      <td>4G</td>
      <td>543472.0</td>
      <td>179203.0</td>
      <td>120-100417</td>
      <td>2963</td>
      <td>6</td>
    </tr>
    <tr>
      <th>24065</th>
      <td>4G</td>
      <td>549570.0</td>
      <td>178310.0</td>
      <td>120-104499</td>
      <td>3136</td>
      <td>6</td>
    </tr>
    <tr>
      <th>24066</th>
      <td>4G</td>
      <td>549570.0</td>
      <td>178310.0</td>
      <td>130-104499</td>
      <td>3136</td>
      <td>6</td>
    </tr>
    <tr>
      <th>24067</th>
      <td>4G</td>
      <td>533981.0</td>
      <td>173987.0</td>
      <td>110-100965</td>
      <td>2330</td>
      <td>8</td>
    </tr>
    <tr>
      <th>24068</th>
      <td>4G</td>
      <td>533981.0</td>
      <td>173987.0</td>
      <td>120-100965</td>
      <td>2330</td>
      <td>8</td>
    </tr>
    <tr>
      <th>24069</th>
      <td>4G</td>
      <td>542957.0</td>
      <td>174403.0</td>
      <td>110-505435</td>
      <td>2939</td>
      <td>4</td>
    </tr>
    <tr>
      <th>24070</th>
      <td>4G</td>
      <td>534937.0</td>
      <td>185068.0</td>
      <td>110-504939</td>
      <td>2418</td>
      <td>2</td>
    </tr>
    <tr>
      <th>24071</th>
      <td>4G</td>
      <td>527299.0</td>
      <td>178679.0</td>
      <td>120-100679</td>
      <td>1208</td>
      <td>3</td>
    </tr>
    <tr>
      <th>24072</th>
      <td>4G</td>
      <td>527299.0</td>
      <td>178679.0</td>
      <td>130-100679</td>
      <td>1208</td>
      <td>3</td>
    </tr>
    <tr>
      <th>24073</th>
      <td>4G</td>
      <td>517848.0</td>
      <td>168960.0</td>
      <td>110-100084</td>
      <td>548</td>
      <td>7</td>
    </tr>
    <tr>
      <th>24074</th>
      <td>4G</td>
      <td>517848.0</td>
      <td>168960.0</td>
      <td>120-100084</td>
      <td>548</td>
      <td>7</td>
    </tr>
    <tr>
      <th>24075</th>
      <td>4G</td>
      <td>526066.0</td>
      <td>178829.0</td>
      <td>110-100422</td>
      <td>1084</td>
      <td>7</td>
    </tr>
    <tr>
      <th>24076</th>
      <td>4G</td>
      <td>526066.0</td>
      <td>178829.0</td>
      <td>130-100422</td>
      <td>1084</td>
      <td>7</td>
    </tr>
    <tr>
      <th>24077</th>
      <td>4G</td>
      <td>532970.0</td>
      <td>159300.0</td>
      <td>110-104456</td>
      <td>2167</td>
      <td>5</td>
    </tr>
    <tr>
      <th>24078</th>
      <td>4G</td>
      <td>532970.0</td>
      <td>159300.0</td>
      <td>130-104456</td>
      <td>2167</td>
      <td>5</td>
    </tr>
    <tr>
      <th>24079</th>
      <td>4G</td>
      <td>527515.0</td>
      <td>179470.0</td>
      <td>110-101008</td>
      <td>1233</td>
      <td>7</td>
    </tr>
    <tr>
      <th>24080</th>
      <td>4G</td>
      <td>527515.0</td>
      <td>179470.0</td>
      <td>120-101008</td>
      <td>1233</td>
      <td>7</td>
    </tr>
    <tr>
      <th>24081</th>
      <td>4G</td>
      <td>532953.0</td>
      <td>180850.0</td>
      <td>110-101252</td>
      <td>2162</td>
      <td>7</td>
    </tr>
    <tr>
      <th>24082</th>
      <td>4G</td>
      <td>532953.0</td>
      <td>180850.0</td>
      <td>120-101252</td>
      <td>2162</td>
      <td>7</td>
    </tr>
    <tr>
      <th>24083</th>
      <td>4G</td>
      <td>537533.0</td>
      <td>180299.0</td>
      <td>110-104546</td>
      <td>2627</td>
      <td>5</td>
    </tr>
    <tr>
      <th>24084</th>
      <td>4G</td>
      <td>537533.0</td>
      <td>180299.0</td>
      <td>120-104546</td>
      <td>2627</td>
      <td>5</td>
    </tr>
    <tr>
      <th>24085</th>
      <td>4G</td>
      <td>530602.0</td>
      <td>180792.0</td>
      <td>110-101418</td>
      <td>1737</td>
      <td>3</td>
    </tr>
    <tr>
      <th>24086</th>
      <td>4G</td>
      <td>530602.0</td>
      <td>180792.0</td>
      <td>120-101418</td>
      <td>1737</td>
      <td>3</td>
    </tr>
    <tr>
      <th>24087</th>
      <td>3G</td>
      <td>523552.0</td>
      <td>176576.0</td>
      <td>21007-18949</td>
      <td>850</td>
      <td>9</td>
    </tr>
    <tr>
      <th>24088</th>
      <td>4G</td>
      <td>531250.0</td>
      <td>181332.0</td>
      <td>110-106591</td>
      <td>1849</td>
      <td>6</td>
    </tr>
    <tr>
      <th>24089</th>
      <td>4G</td>
      <td>536368.0</td>
      <td>181033.0</td>
      <td>110-107090</td>
      <td>2532</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
<p>24090 rows × 6 columns</p>
</div>

