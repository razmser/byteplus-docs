# Usage detail CSV parsing best practices

This document describes how to parse the CSV file at the *DownloadUrl* returned by [Query a usage record export task](get-record-export-task-api.md). It covers field descriptions, switch times, effective rules, `charge_value_list` parsing, multimodal usage coverage, compatibility strategies, and sample code in multiple languages.

## CSV format and version changes

### Switch time and effective rules

`GetRecordExportTask` determines the CSV format used for an export task based on the *EndTime* of the task's query window. The switch time depends on when the user first made a call:

* Users who generated invocations before September 2, 2026: the switch time is September 15, 2026.

* Users who generated new invocations on or after September 2, 2026: the switch time is September 2, 2026.

The effective rules are as follows:

* If *EndTime* is earlier than or equal to the corresponding switch time, the CSV returns the old format.

* If *EndTime* is later than the corresponding switch time, the CSV returns the new format.

After a task is submitted, the CSV field format for that task remains unchanged and does not switch during polling.

### Differences between old and new formats

The request parameters, response parameters, and `DownloadUrl` generation method of the `GetRecordExportTask` API remain unchanged. This change affects only the content format of the CSV file downloaded through `DownloadUrl`.

* **Old format**: 18 columns.

* **New format**: 16 columns.

* **Removed fields**: `input_token_charge_item`, `output_token_charge_item`, and `cache_hit_token_charge_item`.

* **Added field**: `charge_value_list`. This field is located after `volc_instance_id` and before `timestamp`, and is used to return information about each billing item in a unified way.

* **Other fields**: The names, order, and meanings of the other fields remain unchanged.

```text
Old format (18 columns)
... | volc_instance_id | input_token_charge_item | output_token_charge_item | cache_hit_token_charge_item | timestamp | pt_hour

New format (16 columns)
... | volc_instance_id | charge_value_list | timestamp | pt_hour
```

## CSV fields

### New CSV fields

The new\-format CSV file returns fields in the following order.

#### Identifiers and model fields

|Field |Description |
|---|---|
|`req_id` |The business request ID, used to associate a request with its usage details. |
|`uuid` |The unique metering\-record ID, used to identify the same record. |
|`account_id` |The BytePlus account ID to which the usage belongs. |
|`user_id` |The calling user ID in the upstream record. The value is `0` when it is not provided. |
|`model_name` |The name of the model associated with the metering record. |
|`model_version` |The model version identifier. |
|`model_endpoint` |The model endpoint ID associated with the request. |

#### Usage and billing fields

|Field |Description |
|---|---|
|`input_tokens` |The number of input tokens. For text inference, this includes cache\-hit tokens. |
|`output_tokens` |The number of output tokens. Tokens for video generation also appear in this field; they do not represent video duration. |
|`cache_hit_tokens` |The number of cache\-hit tokens in the input. Do not add this value to input usage again. |
|`billing_status` |The billing classification status. The CSV currently returns pay\-as\-you\-go metering details with the `normal` status. |
|`product` |The product code used for billing, sourced from the billing configuration. |
|`volc_instance_id` |The associated transaction or billing instance ID, which differs from the model endpoint ID. |
|`charge_value_list` |The list of billing items. Each element includes `code`, `value`, and `scale`; the metering value for one item is `value / scale`. |

#### Time fields

|Field |Description |
|---|---|
|`timestamp` |The Unix timestamp of the upstream event, in seconds. This is not the CSV export time. |
|`pt_hour` |The hourly data partition in the `YYYY-MM-DD HH:00:00` format. |

### Parse `charge_value_list`

In the new CSV format, the value of `charge_value_list` is a JSON array string. Each element in the array represents a billing item.

For example:

```json
[
  {"code":"example-inference-prompt_ap-southeast-1_realtime","value":54,"scale":1000},
  {"code":"example-inference-completion_ap-southeast-1_realtime","value":5,"scale":1000},
  {"code":"example-inference-KVcache-hit_ap-southeast-1_realtime","value":0,"scale":1000}
]
```

Field descriptions:

* `code`: The billing item name, corresponding to the value of the `*_charge_item` fields in the old format.

* `value`: An integer metering value, such as the number of tokens or another billing metering value.

* `scale`: An integer scaling factor.

The metering value for one billing item is `value / scale`.

## Multimodal usage and field limits

The following information is unavailable from this CSV:

|Information type |Description |
|---|---|
|Audio usage |Audio usage cannot currently be queried from this CSV. Audio token breakdowns and audio duration are not provided. |
|Video generation resolution and duration |The specific resolution and duration of generated videos cannot currently be queried from this CSV. A billing item `code` can help identify the billing tier but cannot reconstruct the exact resolution. |
|Image generation quantity and specifications |The number of generated images, number of input images, pixels, and specifications cannot currently be queried from this CSV. Image\-generation usage is based on billing items in `charge_value_list`. |
|Other business details |Complete business task details cannot currently be queried from this CSV. |

## Parsing and compatibility recommendations

* **Support historical formats**: When handling both old and new CSV files, prefer detecting the format by column name. Only branch by the column count, `18` or `16`, when the parsing logic depends on field positions.

* When the `charge_value_list` field is present: Parse it as JSON first, and then iterate through the array elements to obtain the actual billing value of each billing item.

* If your existing logic depends on the three old columns `input_token_charge_item`, `output_token_charge_item`, and `cache_hit_token_charge_item`: Migrate to reading `charge_value_list`, and then route processing based on `code`.

## Notes

* **Scope of impact**: This change affects only the CSV file content downloaded through *DownloadUrl*. It does not affect the request parameter schema or response parameter schema of the `GetRecordExportTask` API, or the return logic of the `DownloadUrl` field itself.

* We recommend downloading the file as soon as possible after the task is completed and you obtain `DownloadUrl`. The download link is valid for 12 hours. After it expires, submit a new task to obtain a new link.

* **Support historical results**: If your business needs to maintain long\-term compatibility with historical export results, keep parsing branches for both old and new formats. Do not parse only by fixed column positions.

* **Billing note**: Usage in the CSV is for reconciliation only. The final charge is based on the bill in [Billing Center > Bill management](https://console.byteplus.com/finance/bill/detail/).

## Sample code

The following examples read the CSV file at *DownloadUrl*, parse the JSON array in `charge_value_list`, and print the request ID, metering value for each billing item, and the aggregated `total_billing` for every record. You can use the sample code for local troubleshooting, parser validation, and pre\-integration checks.

**Python**

```Python
import csv
import json
import sys


def main() -> None:
    if len(sys.argv) != 2:
        raise SystemExit("Usage: python parse_charge_value_list.py <csv_path>")

    with open(sys.argv[1], newline="", encoding="utf-8") as file:
        reader = csv.DictReader(file)
        for row in reader:
            items = json.loads(row["charge_value_list"])
            total_billing = 0.0

            print(f"req_id={row.get('req_id', '')}")
            for item in items:
                billing = item["value"] / item["scale"]
                total_billing += billing
                print(f"  - {item['code']}: {billing:.6f}")

            print(f"total_billing={total_billing:.6f}")


if __name__ == "__main__":
    main()
```

**Go**

```Go
package main

import (
    "encoding/csv"
    "encoding/json"
    "fmt"
    "io"
    "os"
)

type ChargeValue struct {
    Code  string `json:"code"`
    Value int64  `json:"value"`
    Scale int64  `json:"scale"`
}

func main() {
    if len(os.Args) != 2 {
        panic("usage: go run main.go <csv_path>")
    }

    file, err := os.Open(os.Args[1])
    if err != nil {
        panic(err)
    }
    defer file.Close()

    reader := csv.NewReader(file)
    header, err := reader.Read()
    if err != nil {
        panic(err)
    }

    index := map[string]int{}
    for i, name := range header {
        index[name] = i
    }

    for {
        row, err := reader.Read()
        if err != nil {
            if err == io.EOF {
                break
            }
            panic(err)
        }

        var items []ChargeValue
        if err := json.Unmarshal([]byte(row[index["charge_value_list"]]), &items); err != nil {
            panic(err)
        }

        totalBilling := 0.0
        fmt.Printf("req_id=%s\n", row[index["req_id"]])
        for _, item := range items {
            billing := float64(item.Value) / float64(item.Scale)
            totalBilling += billing
            fmt.Printf("  - %s: %.6f\n", item.Code, billing)
        }
        fmt.Printf("total_billing=%.6f\n", totalBilling)
    }
}
```

**Java**

```Java
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.io.BufferedReader;
import java.io.FileReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

class ParseChargeValueList {
    private static final ObjectMapper OBJECT_MAPPER = new ObjectMapper();

    private static class ChargeValue {
        public String code;
        public double value;
        public double scale;
    }

    public static void main(String[] args) throws Exception {
        if (args.length != 1) {
            throw new IllegalArgumentException("usage: java ParseChargeValueList <csv_path>");
        }

        try (BufferedReader reader = new BufferedReader(new FileReader(args[0]))) {
            List<String> header = parseCsvLine(reader.readLine());
            Map<String, Integer> index = new HashMap<>();

            for (int i = 0; i < header.size(); i++) {
                index.put(header.get(i), i);
            }

            String line;
            while ((line = reader.readLine()) != null) {
                List<String> row = parseCsvLine(line);
                List<ChargeValue> items = OBJECT_MAPPER.readValue(
                        row.get(index.get("charge_value_list")),
                        new TypeReference<List<ChargeValue>>() {}
                );
                double totalBilling = 0.0;

                System.out.println("req_id=" + row.get(index.get("req_id")));
                for (ChargeValue item : items) {
                    double billing = item.value / item.scale;
                    totalBilling += billing;
                    System.out.printf("  - %s: %.6f%n", item.code, billing);
                }
                System.out.printf("total_billing=%.6f%n", totalBilling);
            }
        }
    }

    private static List<String> parseCsvLine(String line) {
        List<String> result = new ArrayList<>();
        StringBuilder current = new StringBuilder();
        boolean inQuotes = false;

        for (int i = 0; i < line.length(); i++) {
            char ch = line.charAt(i);
            if (inQuotes) {
                if (ch == '"') {
                    if (i + 1 < line.length() && line.charAt(i + 1) == '"') {
                        current.append('"');
                        i++;
                    } else {
                        inQuotes = false;
                    }
                } else {
                    current.append(ch);
                }
            } else {
                if (ch == '"') {
                    inQuotes = true;
                } else if (ch == ',') {
                    result.add(current.toString());
                    current.setLength(0);
                } else {
                    current.append(ch);
                }
            }
        }

        result.add(current.toString());
        return result;
    }
}
```

**C++**

```C++
#include <fstream>
#include <iomanip>
#include <iostream>
#include <stdexcept>
#include <string>
#include <unordered_map>
#include <vector>

#include <nlohmann/json.hpp>

struct ChargeValue {
    std::string code;
    double value;
    double scale;
};

std::vector<std::string> ParseCsvLine(const std::string& line) {
    std::vector<std::string> result;
    std::string current;
    bool in_quotes = false;

    for (size_t i = 0; i < line.size(); ++i) {
        char ch = line[i];
        if (in_quotes) {
            if (ch == '"') {
                if (i + 1 < line.size() && line[i + 1] == '"') {
                    current.push_back('"');
                    ++i;
                } else {
                    in_quotes = false;
                }
            } else {
                current.push_back(ch);
            }
        } else {
            if (ch == '"') {
                in_quotes = true;
            } else if (ch == ',') {
                result.push_back(current);
                current.clear();
            } else {
                current.push_back(ch);
            }
        }
    }

    result.push_back(current);
    return result;
}

int main(int argc, char* argv[]) {
    if (argc != 2) {
        throw std::runtime_error("Usage: ./parse_charge_value_list <csv_path>");
    }

    std::ifstream file(argv[1]);
    if (!file.is_open()) {
        throw std::runtime_error("Failed to open CSV file.");
    }

    std::string line;
    if (!std::getline(file, line)) {
        throw std::runtime_error("CSV file is empty.");
    }

    const std::vector<std::string> header = ParseCsvLine(line);
    std::unordered_map<std::string, size_t> index;
    for (size_t i = 0; i < header.size(); ++i) {
        index[header[i]] = i;
    }

    while (std::getline(file, line)) {
        const std::vector<std::string> row = ParseCsvLine(line);
        const auto items = nlohmann::json::parse(row.at(index.at("charge_value_list")));
        double total_billing = 0.0;

        std::cout << "req_id=" << row.at(index.at("req_id")) << '\n';
        for (const auto& item : items) {
            const double billing = item.at("value").get<double>() / item.at("scale").get<double>();
            total_billing += billing;
            std::cout << "  - " << item.at("code").get<std::string>() << ": "
                      << std::fixed << std::setprecision(6) << billing << '\n';
        }
        std::cout << "total_billing=" << std::fixed << std::setprecision(6) << total_billing << '\n';
    }

    return 0;
}
```

The returned result is as follows:

```text
req_id=021738b4-2db3-4c4d-9ec1-6e4d8f3b1a2c
  - example-inference-prompt_ap-southeast-1_realtime: 0.054000
  - example-inference-completion_ap-southeast-1_realtime: 0.005000
  - example-inference-KVcache-hit_ap-southeast-1_realtime: 0.000000
total_billing=0.059000
```
