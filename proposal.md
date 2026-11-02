# Final Project Proposal: Waste Conversion Technology vs. Landfilling in the EU

**Kevin Derderian**, INFO 4617

## 1. Research question

Looking at the European Union from 2005 to 2023, did countries that increased waste-to-energy (WTE) incineration reduce their use of landfilling, or did WTE come at the expense of recycling?

### Intended audience

This study would be useful for state officials deciding whether to shift their focus from landfilling waste to WTE. Small and large waste companies operating in the United States are also looking to change their company goals to a reliable sustainability option, one that lets them profit from energy generation on top of end-of-life waste handling.

### Expectation

I expect to find that waste-conversion incineration technology correlates with decreased landfilling and increased generation efficiency. I would be surprised if WTE had a negative impact on recycling.

## 2. Data sources and method

I will collect data from Eurostat's *Municipal waste by waste management operations* dataset.

- Data browser: <https://ec.europa.eu/eurostat/databrowser/view/env_wasmun/default/table>
- API endpoint: <https://ec.europa.eu/eurostat/api/dissemination/statistics/1.0/data/env_wasmun>. The API is public and needs no key.

I will apply the practices from Chapter 5 (Protocols and HTTP) to collect the data through the Eurostat API with `requests.get(url, ...)`, checking status codes and plotting the data over time. I will also apply the Chapter 4 practices for XML and JSON, using the "peel the onion" strategy from the "Handling deeply nested data" section: exploring the response with `type()`, `.keys()` and `.get()`, building my own dictionaries, and loading them into a `pd.DataFrame`.

### Scope

The scope is the 27 European Union countries from 2005 to 2023 (19 years). I think this is a large enough dataset to show concrete evidence of change in landfill reduction.

### Analysis

I will compare the amounts of municipal solid waste sent to each treatment route (landfill, energy recovery and recycling) to see whether countries that use WTE differ from countries that don't. I will build side-by-side bar charts to compare these routes and use them to test my hypothesis.

## 3. Ethics and feasibility

### Terms of service and robots.txt

Eurostat data can be used freely for research and commercial purposes, as long as the source is acknowledged and properly cited ([Eurostat: Our policies](https://ec.europa.eu/eurostat/about-us/policies/copyright)). The API is built for automated access by students and researchers, which is why it is free and open to everyone.

### Personal information

My project collects no personal information. Following Chapter 2, I will identify my requests to the API with a descriptive User-Agent: `WebDataScience/1.0 (INFO 4617; kevin.derderian@colorado.edu)`.

### One record retrieved

```python
import requests

HEADERS = {
    "User-Agent": "WebDataScience/1.0 (INFO 4617; kevin.derderian@colorado.edu)"
}

url = "https://ec.europa.eu/eurostat/api/dissemination/statistics/1.0/data/env_wasmun"
params = {"geo": "DK", "time": "2022"}

response = requests.get(url, params=params, headers=HEADERS)
print(response.status_code)

data = response.json()
print(type(data))
print(data.keys())
print(data.get("value"))
print(data.get("dimension").keys())
```

Output:

```text
200
<class 'dict'>
dict_keys(['version', 'class', 'label', 'source', 'updated', 'value', 'id', 'size', 'dimension', 'extension'])
{'8': 0, '9': 0, '4': 363, '5': 2141, '6': 12, '7': 70, '0': 759, '1': 4478, '18': 3, '19': 21, '10': 363, '11': 2141, '12': 382, '13': 2252, '16': 181, '17': 1070, '14': 197, '15': 1162, '2': 759, '3': 4478}
dict_keys(['freq', 'wst_oper', 'unit', 'geo', 'time'])
```

### Biggest risk

The API may not have complete data for every country and year in my range. If the 2005–2023 coverage is too sparse, I will shorten the range to 2015–2023. That gives a smaller pool of data, but a more current one.

### AI tools

I used AI to help find online data sources for this project. It pointed me to Eurostat, the EU's statistics office, which publishes this data for free, and suggested exploring the `env_wasmun` table in the Eurostat data browser first (choosing countries, years and treatment types such as landfill, energy recovery and recycling) to understand the data before writing code.
