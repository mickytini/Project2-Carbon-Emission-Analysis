# Project2-Carbon-Emission-Analysis
## We have the following tables:
### Table 'product_emissions'

    SELECT * FROM product_emissions LIMIT 5;

| id           | company_id | country_id | industry_group_id | year | product_name                                                    | weight_kg | carbon_footprint_pcf | upstream_percent_total_pcf | operations_percent_total_pcf | downstream_percent_total_pcf | 
| -----------: | ---------: | ---------: | ----------------: | ---: | --------------------------------------------------------------: | --------: | -------------------: | -------------------------: | ---------------------------: | ---------------------------: | 
| 10056-1-2014 | 82         | 28         | 2                 | 2014 | Frosted Flakes(R) Cereal                                        | 0.7485    | 2                    | 57.50                      | 30.00                        | 12.50                        | 
| 10056-1-2015 | 82         | 28         | 15                | 2015 | "Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)" | 0.7485    | 2                    | 57.50                      | 30.00                        | 12.50                        | 
| 10222-1-2013 | 83         | 28         | 8                 | 2013 | Office Chair                                                    | 20.68     | 73                   | 80.63                      | 17.36                        | 2.01                         | 
| 10261-1-2017 | 14         | 16         | 25                | 2017 | Multifunction Printers                                          | 110       | 1488                 | 30.65                      | 5.51                         | 63.84                        | 
| 10261-2-2017 | 14         | 16         | 25                | 2017 | Multifunction Printers                                          | 110       | 1818                 | 25.08                      | 4.51                         | 70.41                        | 

### Table 'industry_groups'

    SELECT * FROM industry_groups LIMIT 5;

| id | industry_group                                                         | 
| -: | ---------------------------------------------------------------------: | 
| 1  | "Consumer Durables, Household and Personal Products"                   | 
| 2  | "Food, Beverage & Tobacco"                                             | 
| 3  | "Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber" | 
| 4  | "Mining - Iron, Aluminum, Other Metals"                                | 
| 5  | "Pharmaceuticals, Biotechnology & Life Sciences"                       | 

### Table 'companies'

    SELECT * FROM companies LIMIT 5;

| id | company_name                  | 
| -: | ----------------------------: | 
| 1  | "Autodesk, Inc."              | 
| 2  | "Casio Computer Co., Ltd."    | 
| 3  | "Cisco Systems, Inc."         | 
| 4  | "CNX Coal Resources, LP"      | 
| 5  | "Coca-Cola Enterprises, Inc." | 

### Table 'countries'

    SELECT * FROM countries LIMIT 5;

| id | country_name | 
| -: | -----------: | 
| 1  | Australia    | 
| 2  | Belgium      | 
| 3  | Brazil       | 
| 4  | Canada       | 
| 5  | Chile        | 

## Some questions to research and answer
### Which products contribute the most to carbon emissions?
    SELECT 
        product_name,
	ROUND(AVG(carbon_footprint_pcf), 2) as 'Average PCF'
    FROM
        product_emissions
    GROUP BY
        product_name
    ORDER BY ROUND(AVG(carbon_footprint_pcf), 2) DESC
    LIMIT 10;

| product_name                                                                                                                       | Average PCF | 
| ---------------------------------------------------------------------------------------------------------------------------------: | ----------: | 
| Wind Turbine G128 5 Megawats                                                                                                       | 3718044.00  | 
| Wind Turbine G132 5 Megawats                                                                                                       | 3276187.00  | 
| Wind Turbine G114 2 Megawats                                                                                                       | 1532608.00  | 
| Wind Turbine G90 2 Megawats                                                                                                        | 1251625.00  | 
| Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | 191687.00   | 
| Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | 167000.00   | 
| TCDE                                                                                                                               | 99075.00    | 
| Mercedes-Benz GLE (GLE 500 4MATIC)                                                                                                 | 91000.00    | 
| Mercedes-Benz S-Class (S 500)                                                                                                      | 85000.00    | 
| Mercedes-Benz SL (SL 350)                                                                                                          | 72000.00    | 

### What are the industry groups of these products?
    SELECT
        ind_gr.industry_group AS 'Industry Group',
        prod_em.product_name AS 'Product name',
        ROUND(AVG(prod_em.carbon_footprint_pcf), 2) AS 'Average PCF'
    FROM
        product_emissions AS prod_em
    LEFT JOIN
        industry_groups AS ind_gr ON prod_em.industry_group_id = ind_gr.id
    GROUP BY prod_em.product_name
    ORDER BY ROUND(AVG(prod_em.carbon_footprint_pcf), 2) DESC
    LIMIT 10;

| Industry Group                     | Product name                                                                                                                       | Average PCF | 
| ---------------------------------: | ---------------------------------------------------------------------------------------------------------------------------------: | ----------: | 
| Electrical Equipment and Machinery | Wind Turbine G128 5 Megawats                                                                                                       | 3718044.00  | 
| Electrical Equipment and Machinery | Wind Turbine G132 5 Megawats                                                                                                       | 3276187.00  | 
| Electrical Equipment and Machinery | Wind Turbine G114 2 Megawats                                                                                                       | 1532608.00  | 
| Electrical Equipment and Machinery | Wind Turbine G90 2 Megawats                                                                                                        | 1251625.00  | 
| Automobiles & Components           | Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.                                                                 | 191687.00   | 
| Materials                          | Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall | 167000.00   | 
| Materials                          | TCDE                                                                                                                               | 99075.00    | 
| Automobiles & Components           | Mercedes-Benz GLE (GLE 500 4MATIC)                                                                                                 | 91000.00    | 
| Automobiles & Components           | Mercedes-Benz S-Class (S 500)                                                                                                      | 85000.00    | 
| Automobiles & Components           | Mercedes-Benz SL (SL 350)                                                                                                          | 72000.00    | 

### What are the industries with the highest contribution to carbon emissions?
    SELECT 
        ind.industry_group, 
        SUM(p.carbon_footprint_pcf) AS total_carbon_footprint
    FROM 
        product_emissions AS p
    JOIN 
        industry_groups ind ON p.industry_group_id = ind.id
    GROUP BY 
        ind.industry_group
    ORDER BY 
        total_carbon_footprint DESC
    LIMIT 10;

| industry_group                                   | total_carbon_footprint | 
| -----------------------------------------------: | ---------------------: | 
| Electrical Equipment and Machinery               | 9801558                | 
| Automobiles & Components                         | 2582264                | 
| Materials                                        | 577595                 | 
| Technology Hardware & Equipment                  | 363776                 | 
| Capital Goods                                    | 258712                 | 
| "Food, Beverage & Tobacco"                       | 111131                 | 
| "Pharmaceuticals, Biotechnology & Life Sciences" | 72486                  | 
| Chemicals                                        | 62369                  | 
| Software & Services                              | 46544                  | 
| Media                                            | 23017                  | 

### What are the companies with the highest contribution to carbon emissions?
    SELECT 
        c.company_name, 
        SUM(p.carbon_footprint_pcf) AS total_carbon_footprint
    FROM 
        product_emissions AS p
    JOIN 
        companies AS c ON p.company_id = c.id
    GROUP BY 
        c.company_name
    ORDER BY 
        total_carbon_footprint DESC
    LIMIT 10;

| company_name                            | total_carbon_footprint | 
| --------------------------------------: | ---------------------: | 
| "Gamesa Corporación Tecnológica, S.A."  | 9778464                | 
| Daimler AG                              | 1594300                | 
| Volkswagen AG                           | 655960                 | 
| "Mitsubishi Gas Chemical Company, Inc." | 212016                 | 
| "Hino Motors, Ltd."                     | 191687                 | 
| Arcelor Mittal                          | 167007                 | 
| Weg S/A                                 | 160655                 | 
| General Motors Company                  | 137007                 | 
| "Lexmark International, Inc."           | 132012                 | 
| "Daikin Industries, Ltd."               | 105600                 | 

### What are the countries with the highest contribution to carbon emissions?
    SELECT 
    	cntr.country_name AS 'Country name',
    	ROUND(SUM(prod_em.carbon_footprint_pcf), 2) AS 'Average PCF'
    FROM 
    	product_emissions AS prod_em
    LEFT JOIN
    	countries AS cntr ON prod_em.country_id = cntr.id
    GROUP BY cntr.country_name 
    ORDER BY ROUND(SUM(prod_em.carbon_footprint_pcf), 2) DESC
    LIMIT 10;

| Country name | Average PCF | 
| -----------: | ----------: | 
| Spain        | 9786130.00  | 
| Germany      | 2251225.00  | 
| Japan        | 653237.00   | 
| USA          | 518381.00   | 
| South Korea  | 186965.00   | 
| Brazil       | 169337.00   | 
| Luxembourg   | 167007.00   | 
| Netherlands  | 70417.00    | 
| Taiwan       | 62875.00    | 
| India        | 24574.00    | 

### What is the trend of carbon footprints (PCFs) over the years?
    SELECT 
        pe.year, 
        SUM(pe.carbon_footprint_pcf) AS total_carbon_footprint
    FROM 
        product_emissions pe
    GROUP BY 
        pe.year
    ORDER BY 
        pe.year;

| year | total_carbon_footprint | 
| ---: | ---------------------: | 
| 2013 | 503857                 | 
| 2014 | 624226                 | 
| 2015 | 10840415               | 
| 2016 | 1640182                | 
| 2017 | 340271                 | 

### Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?
    SELECT 
        ig.industry_group,
        (earliest_year_footprint - latest_year_footprint) AS decrease
    FROM 
        (
            SELECT 
                pe.industry_group_id,
                SUM(CASE WHEN pe.year = (SELECT MAX(year) FROM product_emissions) THEN pe.carbon_footprint_pcf END) AS latest_year_footprint,
                SUM(CASE WHEN pe.year = (SELECT MIN(year) FROM product_emissions) THEN pe.carbon_footprint_pcf END) AS earliest_year_footprint
            FROM 
                product_emissions pe
            GROUP BY 
                pe.industry_group_id
        ) AS FootprintChange
    JOIN 
        industry_groups ig ON FootprintChange.industry_group_id = ig.id
    WHERE 
        latest_year_footprint < earliest_year_footprint
    ORDER BY 
        decrease DESC;

| industry_group                     | decrease | 
| ---------------------------------: | -------: | 
| Technology Hardware & Equipment    | 33508    | 
| "Food, Beverage & Tobacco"         | 1833     | 
| Commercial & Professional Services | 416      | 





