# Table Join Process

A WPS Process to execute a table join between two GeoServer layers or datasets.

## Building

Set the required versions of GeoTools and GeoServer in the `pom.xml` file and then

    mvn clean install
    mvn assembly:single
    
will generate a zip file called `target/tablejoin-0.0.1-SNAPSHOT-bin.zip` which can be unpacked into the GeoServer `WEB-INF/lib` directory.

## Usage

You can specify the input data sets by reference or by explict listing in the request. You must also provide a filter that is the
equivalent of an SQL where clause. It should provide a filter which is true when you want a row joined to another, if multiple 
rows match the first one found will be used. If there is no match then the row will be omitted from the output.

Try a simple join with an XML file like this, it will join the provided CSV data with the USA States Layer using the filter
`"STATE_NAME" = "State"` as the join:

    <?xml version="1.0" encoding="UTF-8"?><wps:Execute version="1.0.0" service="WPS" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://www.opengis.net/wps/1.0.0" xmlns:wfs="http://www.opengis.net/wfs" xmlns:wps="http://www.opengis.net/wps/1.0.0" xmlns:ows="http://www.opengis.net/ows/1.1" xmlns:gml="http://www.opengis.net/gml" xmlns:ogc="http://www.opengis.net/ogc" xmlns:wcs="http://www.opengis.net/wcs/1.1.1" xmlns:xlink="http://www.w3.org/1999/xlink" xsi:schemaLocation="http://www.opengis.net/wps/1.0.0 http://schemas.opengis.net/wps/1.0.0/wpsAll.xsd">
      <ows:Identifier>TableJoin:simpleJoinTables</ows:Identifier>
      <wps:DataInputs>
        <wps:Input>
          <ows:Identifier>target</ows:Identifier>
          <wps:Reference mimeType="text/xml" xlink:href="http://geoserver/wfs" method="POST">
            <wps:Body>
              <wfs:GetFeature service="WFS" version="1.0.0" outputFormat="GML2" xmlns:topp="http://www.openplans.org/topp">
                <wfs:Query typeName="topp:states"/>
              </wfs:GetFeature>
            </wps:Body>
          </wps:Reference>
        </wps:Input>
        <wps:Input>
          <ows:Identifier>source</ows:Identifier>
          <wps:Data>
            <wps:ComplexData mimeType="text/csv"><![CDATA[State,inc1980,inc1990,inc1995,inc2000,inc2003,inc2006,inc2009,inc2012
    Alabama,7465,14899,19683,23521,26338,30894,33096,35625
    Alaska,13007,20887,25798,29642,33568,38138,42603,46778
    Arizona,8854,16262,20634,24988,26838,31936,32935,35979
    Arkansas,7113,13779,18546,21995,24289,28473,31946,34723
    California,11021,20656,24496,32149,33749,39626,42325,44980
    Colorado,10143,18818,24865,32434,34283,39491,41344,45135
    Connecticut,11532,25426,31947,40702,43173,50762,54397,58908
    Delaware,10059,19719,25391,31012,32810,39131,39817,41940
    District of Columbia,12251,24643,33045,38838,48342,57746,66000,74710
    Florida,9246,18785,23512,27764,30446,36720,37780,40344
    Georgia,8021,17121,22230,27794,29442,32095,33786,36869
    Hawaii,10129,20905,25584,27851,30913,37023,42009,44024
    Idaho,8105,15304,19630,23727,25911,29920,31632,33749
    Illinois,10454,20159,25643,31856,33690,38409,41411,44815
    Indiana,8914,16815,21845,26933,28783,32288,33725,36902
    Iowa,9226,16683,21181,26431,29043,33038,36751,42126
    Kansas,9880,17639,21889,27374,29935,34799,37916,41835
    Kentucky,7679,14751,19215,24085,26252,29729,31883,35041
    Louisiana,8412,14279,19541,23090,26100,31821,35507,39413
    Maine,7760,17041,20240,25380,28831,32095,36745,39481
    Maryland,10394,22088,26896,33482,37331,43788,48285,51971
    Massachusetts,10103,22248,28051,37704,39815,46299,49875,54687
    Michigan,9801,18239,23975,29127,30439,33788,34025,37497
    Minnesota,9673,18784,24583,31935,34443,38859,41552,46227
    Mississippi,6573,12578,17185,20900,23448,27028,30103,33073
    Missouri,8812,17407,22094,27206,29252,32789,35676,39049
    Montana,8342,14743,18764,22518,25920,30790,34004,37370
    Nebraska,8895,17379,22196,27630,30758,34440,38081,43143
    Nevada,10848,20248,25808,29506,31266,38994,38578,37361
    New Hampshire,9150,20231,25008,33169,34702,39753,42831,47058
    New Jersey,10966,24182,29277,37118,40427,46763,50313,53628
    New Mexico,7940,14213,18852,21931,25541,29929,32992,35079
    New York,10179,22322,27721,34689,36574,44027,46957,52095
    North Carolina,7780,16284,21938,26882,28235,32247,34453,37049
    North Dakota,8642,15320,19084,24708,29204,32763,39530,51893
    Ohio,9399,17547,22887,27977,29944,33320,35381,39289
    Oklahoma,9018,15117,19394,23650,26656,32391,35268,39006
    Oregon,9309,17201,22668,27660,29340,33299,35667,38786
    Pennsylvania,9353,18884,23738,29504,31998,36825,39578,43616
    Rhode Island,9227,19035,24046,29113,31916,37523,41003,44990
    South Carolina,7392,15101,19473,24000,26132,29767,31799,34266
    South Dakota,7800,15628,19848,25958,29234,32030,36935,43659
    Tennessee,7711,15903,21800,25946,28455,32172,34089,37678
    Texas,9439,16747,21526,27752,29372,35166,36484,41471
    Utah,7671,14063,18858,23436,24977,29406,30875,34601
    Vermont,7957,17444,21359,26848,30740,34871,38503,42994
    Virginia,9413,19543,24456,31120,33671,39540,43874,47082
    Washington,10256,19268,23878,31230,33332,38212,41751,45413
    West Virginia,7764,13964,17913,21738,24379,28206,32219,34477
    Wisconsin,9364,17399,22573,28100,30898,34405,36822,40537
    Wyoming,11018,16905,21514,27372,32808,40655,45705,48670]]></wps:ComplexData>
          </wps:Data>
        </wps:Input>
        <wps:Input>
          <ows:Identifier>joinfilter</ows:Identifier>
          <wps:Data>
            <wps:LiteralData>"STATE_NAME" = "State"</wps:LiteralData>
          </wps:Data>
        </wps:Input>
      </wps:DataInputs>
      <wps:ResponseForm>
        <wps:RawDataOutput mimeType="application/json">
          <ows:Identifier>result</ows:Identifier>
        </wps:RawDataOutput>
      </wps:ResponseForm>
    </wps:Execute>
