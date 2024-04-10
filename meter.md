## meter外部函数学习
在github P4-learning 文件夹里有meter相关介绍实验，主要有两个：
- **`exercises/04-RSVP/thrift`** https://github.com/nsg-ethz/p4-learning/tree/0ddba187f207e24a8a614f5d1abf8bc11998c9d3/exercises/04-RSVP/thrift/solution
主要应用meter做服务资源预留和区分服务
- **`examples/meter`** https://github.com/nsg-ethz/p4-learning/tree/master/examples/meter
是meter的应用

**`meter`** 有两种，一种是 **`indirect_meter`**，另一种是  **`direct_meter`** 。有两种类型`MeterType.packets` 、`MeterType.bytes`按数据包统计或按字节统计，

`P4-learning/examples/meter`中对于每个传入数据包，应用`m_read`表，基于数据包的源 MAC 地址进行测量。基于观察到此发送方的流量速率和`meter`的配置，产生 3 个值之一：`0`GREEN、`1`YELLOW 或 `2`RED 放到`meta.meter_tag`。后续应用表`m_filter`对不同`meta.meter_tag`执行操作（观察流表得到是为`0`则无操作，其他的直接默认`drop`。
**`direct_meter`** 是与表条目关联，而 **`indirect_meter`** 可以是按索引寻址。

### **`indirect_meter`** 用法
````C
//定义indirect_meter
meter(32w16384, MeterType.packets) my_meter;
//匹配的table
table m_read {
        key = {
            hdr.ethernet.srcAddr: exact;
        }
        actions = {
            m_action;
            NoAction;
        }
        default_action = NoAction;
        size = 16384;
    }
//执行的action，table会返回meter_index，颜色值返回原数据meta.meter_tag
action m_action(bit<32> meter_index) {
        my_meter.execute_meter<bit<32>>(meter_index, meta.meter_tag);
    }

````
### **`direct_meter`** 用法
````C
//定义一个direct_meter
 direct_meter<bit<32>>(MeterType.packets) my_meter;
//匹配的table，应用direct必须在table中添加meters = 你的meter的名字;
 table m_read {
        key = {
            hdr.ethernet.srcAddr: exact;
        }
        actions = {
            m_action;
            NoAction;
        }
        default_action = NoAction;
        meters = my_meter;
        size = 16384;
    }
//read操作只能在匹配的表项的结果中调用
//执行的action，table会返回meter_index，颜色值返回原数据meta.meter_tag
action m_action() {
        my_meter.read(meta.meter_tag);
    }
````
### **`meter`** 的流表下发

#### ONOS控制器配置meter(Chat-GPT写的，不保真)
ONOS 的 Northbound (北向) API 提供了 REST API 接口，使得可以通过 HTTP 请求来管理网络中的 Meter。这包括添加、删除、查询和修改 Meter 设置。
以下是一些使用 ONOS API 来操作 Meter 的例子：
添加 Meter:
````bash
curl -X POST -u onos:rocks -H "Content-Type: application/json" "http://<onos-ip>:8181/onos/v1/meters/<deviceId>" -d '
{
    "unit": "KB_PER_SEC",
    "bands": [
        {
            "type": "DROP",
            "rate": 1000,
            "burstSize": 100
        }
    ]
}'
````
这里 <onos-ip> 是你的 ONOS 控制器的 IP 地址，而 <deviceId> 是你想配置 Meter 的网络设备的 ID。
查询 Meter:
````bash
curl -X GET -u onos:rocks "http://<onos-ip>:8181/onos/v1/meters/<deviceId>"
````
删除 Meter:
````bash
curl -X DELETE -u onos:rocks "http://<onos-ip>:8181/onos/v1/meters/<deviceId>/<meterId>"
````
在这个例子中，<meterId> 是要删除的 Meter 的 ID。
使用 ONOS API 时，请确保：
ONOS 控制器运行并可访问。
ONOS 控制器的 REST API 已启用。
你有用于验证的有效 ONOS 用户凭据，不同安装的默认凭据可能有所不同。
已经对网络设备进行初始化和配置，使其可以接受来自 ONOS 的指令。
你指定的 <deviceId> 和 <meterId> 与你的网络设备配置相对应。
这些 API 调用示例可能需要根据你的 ONOS 控制器版本以及你的具体需求进行调整。建议参照 ONOS 的官方文档来获取当前版本最准确的 API 信息和详细指南。

#### BMv2配置meter
如果使用`P4/tutorials`做实验，可以将`topology.json`里的`switches`使用`cli_input`，在调用`run_exercise.py`的时候可以使用`subprocess`应用`simple_switch_CLI`配置。
最终应用的是`/home/sinet/P4/behavioral-model/targets/simple_switch/runtime_CLI`
***
- **`do_meter_array_set_rates(self, line)`**:此函数用于为整个计量器数组配置速率。它接受一个字符串参数，包括计量器数组的名称以及以冒号分隔的速率和突发值对。它解析这些值，验证提供的速率数量是否与预期的速率数量相匹配，构造新的速率配置，并使用`client.bm_meter_array_set_rates` `RPC`调用应用它们。

- 这段代码定义了一个名为do_meter_array_set_rates的函数，它接受一个参数line作为输入。函数的主要功能是配置一个完整的计量器数组（meter array）的速率。函数的使用方式是通过命令行输入一系列速率值，并将其应用于指定的计量器数组。下面是对函数的逐行解释：
```
args = line.split(): 将输入的line按空格分割成一个参数列表，并将其赋给args变量。
self.at_least_n_args(args, 1): 检查参数列表，确保至少有一个参数。at_least_n_args是函数中的一个辅助方法，用于验证参数数量。
meter_name = args[0]: 将参数列表中的第一个参数作为计量器数组的名称，将其赋给meter_name变量。
meter = self.get_res("meter", meter_name, ResType.meter_array): 使用get_res方法获取指定名称的计量器数组对象，并将其赋给meter变量。该方法是类中的一个辅助方法。
rates = args[1:]: 将参数列表中除了第一个参数外的所有参数作为速率列表，将其赋给rates变量。
if len(rates) != meter.rate_count: ...: 检查速率列表的长度是否与计量器数组的速率数目相匹配，如果不匹配，则抛出一个异常。该异常表示速率数量错误。
new_rates = []: 创建一个空列表new_rates，用于存储解析后的速率配置。
for rate in rates: ...: 对速率列表进行循环迭代。
r, b = rate.split(':'): 将每个速率项按冒号分割成两部分，分别赋给r和b变量。其中r表示速率值，b表示突发值。
r = float(r): 将速率值转换为浮点数类型。
b = int(b): 将突发值转换为整数类型。
new_rates.append(BmMeterRateConfig(r, b)): 根据解析后的速率值和突发值创建一个BmMeterRateConfig对象，并将其添加到new_rates列表中。
self.client.bm_meter_array_set_rates(0, meter.name, new_rates): 调用client对象的bm_meter_array_set_rates方法，将计量器数组的索引、名称和新的速率配置作为参数，以配置计量器数组的速率。
该函数的作用是根据命令行输入的速率配置，将新的速率应用于指定的计量器数组，并通过调用bm_meter_array_set_rates方法实现配置的下发。
```
***
- **`complete_meter_array_set_rates(self, text, line, start_index, end_index)`**：关于`meter_array_set_rates`命令的命令行自动补全的辅助函数。
***
- **`do_meter_set_rates(self, line)`**：此函数按索引为计量器数组中的单个计量器配置速率。类似于第一个函数，它处理一行参数，第一个参数是计量器的名称，第二个是计量器数组中的索引，其余的是速率:突发值对。它还验证输入并使用`client.bm_mt_set_meter_rates`（如果计量器直接与一个表关联）或`client.bm_meter_set_rates`（否则）设置计量器速率。
***
- **`complete_meter_set_rates(self, text, line, start_index, end_index)`**：此函数为`meter_set_rates`命令提供自动补全。
***
- **`do_meter_get_rates(self, line)`**：检索指定计量器在给定索引处的当前配置速率。它处理参数（期望有两个：计量器名称和它的索引），执行适当的客户端API调用，这取决于计量器是直接还是间接的，并打印计量器速率配置。
***
- **`complete_meter_get_rates(self, text, line, start_index, end_index)`**：此函数可能为meter_get_rates命令提供自动补全。
***
- **`_complete_meters(self, text)`**：此函数可能支持与计量器相关的命令的自动补全，也许通过基于用户输入的部分文本提供计量器名称或索引列表。
***
***代码源码***

````python
def do_meter_array_set_rates(self, line):
        "Configure rates for an entire meter array: meter_array_set_rates <name> <rate_1>:<burst_1> <rate_2>:<burst_2> ..."
        args = line.split()
        self.at_least_n_args(args, 1)
        meter_name = args[0]
        meter = self.get_res("meter", meter_name, ResType.meter_array)
        rates = args[1:]
        if len(rates) != meter.rate_count:
            raise UIn_Error(
                "Invalid number of rates, expected %d but got %d"
                % (meter.rate_count, len(rates))
            )
        new_rates = []
        for rate in rates:
            try:
                r, b = rate.split(':')
                r = float(r)
                b = int(b)
                new_rates.append(BmMeterRateConfig(r, b))
            except:
                raise UIn_Error("Error while parsing rates")
        self.client.bm_meter_array_set_rates(0, meter.name, new_rates)

    def complete_meter_array_set_rates(self, text, line, start_index, end_index):
        return self._complete_meters(text)

    @handle_bad_input
    def do_meter_set_rates(self, line):
        "Configure rates for a meter: meter_set_rates <name> <index> <rate_1>:<burst_1> <rate_2>:<burst_2> ...\nRate uses units/microsecond and burst uses units where units is bytes or packets"
        args = line.split()
        self.at_least_n_args(args, 2)
        meter_name = args[0]
        meter = self.get_res("meter", meter_name, ResType.meter_array)
        try:
            index = int(args[1])
        except:
            raise UIn_Error("Bad format for index")
        rates = args[2:]
        if len(rates) != meter.rate_count:
            raise UIn_Error(
                "Invalid number of rates, expected %d but got %d"
                % (meter.rate_count, len(rates))
            )
        new_rates = []
        for rate in rates:
            try:
                r, b = rate.split(':')
                r = float(r)
                b = int(b)
                new_rates.append(BmMeterRateConfig(r, b))
            except:
                raise UIn_Error("Error while parsing rates")
        if meter.is_direct:
            table_name = meter.binding
            self.client.bm_mt_set_meter_rates(0, table_name, index, new_rates)
        else:
            self.client.bm_meter_set_rates(0, meter.name, index, new_rates)

    def complete_meter_set_rates(self, text, line, start_index, end_index):
        return self._complete_meters(text)

    @handle_bad_input
    def do_meter_get_rates(self, line):
        "Retrieve rates for a meter: meter_get_rates <name> <index>"
        args = line.split()
        self.exactly_n_args(args, 2)
        meter_name = args[0]
        meter = self.get_res("meter", meter_name, ResType.meter_array)
        try:
            index = int(args[1])
        except:
            raise UIn_Error("Bad format for index")
        # meter.rate_count
        if meter.is_direct:
            table_name = meter.binding
            rates = self.client.bm_mt_get_meter_rates(0, table_name, index)
        else:
            rates = self.client.bm_meter_get_rates(0, meter.name, index)
        if len(rates) != meter.rate_count:
            print("WARNING: expected", meter.rate_count, "rates", end=' ')
            print("but only received", len(rates))
        for idx, rate in enumerate(rates):
            print("{}: info rate = {}, burst size = {}".format(
                idx, rate.units_per_micros, rate.burst_size))
````
**以下内容是Chat-GPT 4的回答，他是一个大傻逼，因为他的回答是错误的**
BMv2 本体并没有直接的 API 来配置 Meter。但是，当你使用诸如 ONOS、P4Runtime等控制平面与 BMv2 交互时，这些控制平面接口提供了通过控制器来配置 BMv2 交换机上的 Meter 的能力。

举个例子，如果你通过 P4Runtime API 与 BMv2 交云机交互，你可以编写 P4Runtime 的客户端代码来实现 Meter 的配置。这通常涉及到发送 MeterEntry 更新消息到 BMv2 模拟交换机。

以下是使用 P4Runtime API 配置 BMv2 交换机上的 Meter 的一个基础的概念：
- 连接: 与 BMv2 交互前，需要建立一个到 BMv2 的 gRPC 连接。
- Meter 配置: 创建一个 MeterEntry 消息来配置特定的 Meter。
- 写入: 通过 P4Runtime API 的 Write RPC 调用将 Meter 配置写入 BMv2。

在 P4Runtime，Meter 的配置与 BMv2 的 SimpleSwitchGrpc 目标一同使用。这需要 P4 程序被编译来支持此目标，并且 BMv2 运行时需要有相应的 P4Runtime 服务接口。

另外，Simple_switch_CLI 是 BMv2 的一个组件，它提供了一组 CLI 命令用于交互式地配置和监控 BMv2 交换机。虽然 Simple_switch_CLI 没有提供专门的 Meter 配置命令，但它允许操作交换机中的许多其他功能（如表格和寄存器）。

通常情况下，Meter 的配置和管理由控制器（如 ONOS, P4Runtime API 等）通过适合的南向接口向 BMv2 发送指令来完成。要配置 BMv2 交换机上的 Meter，你可以通过 P4Runtime API 或者其他南向接口来实现规则的添加、删除和修改。这通常涉及到控制器中的网络应用编写相关的网络控制逻辑和消息序列化与反序列化。

#### P4Runtime配置meter(Chat-GPT写的，不保真)
在P4Runtime API中，配置Meter的操作是通过MeterEntry消息在WriteRequest中进行的。MeterEntry被用来表示控制器对于交换机中的一个Meter对象的配置。P4Runtime API是一个protobuf和gRPC基于的API，用于控制支持P4Runtime的交换机（包括BMv2交换机）。

以下是利用P4Runtime API配置Meter的基本步骤：

- 构建MeterEntry消息：这个消息需要包括识别Meter的信息（例如Meter ID或名称）以及Meter的配置参数，比如速率（rate）和突发大小（burst_size）。
- 创建WriteRequest消息：将MeterEntry加入到一个WriteRequest消息中。这个请求可以包括一个或多个更新操作，每一个都有其自己的EntityType和Entity。
- 发送gRPC请求：通过gRPC将WriteRequest发送给交换机。这通常需要与交换机的P4Runtime服务建立一个gRPC通道，并由控制器发送请求。

这里有一个简化的Meter配置的例子，使用了P4Runtime的protobuf定义：
````protobuf
message MeterEntry {
    uint32 meter_id = 1; // Meter的ID，来自于P4程序的定义
    // 重复字段，支持对全部Meter条目或单个Meter条目的配置
    repeated int64 index = 2; // Meter条目的索引
    enum Config {
        UNKNOWN = 0;
        COLOR_AWARE = 1;
        COLOR_UNAWARE = 2;
    }
    // Meter的类型，基于颜色的感知或不感知
    Config config = 3;
    // Meter配置的细节，如速率和突发大小
    message MeterBand {
        uint64 rate = 1; // 速率
        uint64 burst_size = 2; // 突发大小
    }
    repeated MeterBand meter_band = 4; // Meter的带宽配置
}

message WriteRequest {
    uint64 device_id = 1; // 设备ID
    // 其他相关信息 ...

    // 实体更新列表
    repeated Update updates = 10;
    message Update {
        enum Type {
            UNSPECIFIED = 0;
            INSERT = 1;
            MODIFY = 2;
            DELETE = 3;
        }
        Type type = 1;
        Entity entity = 2;
    }
    message Entity {
        // 选择其中一项用于更新
        oneof entity {
            // 其他entity类型...
            MeterEntry meter_entry = 7; // MeterEntry
        }
    }
}
````
控制器则需要将这个MeterEntry包装进WriteRequest并通过P4Runtime gRPC接口发送给交换机。Meter更新可以是插入（INSERT），修改（MODIFY），或删除（DELETE）。

这个流程需要正确的protobuf和gRPC接口设置，以及与P4Runtime服务的通信配置。这些操作通常由网络控制器软件来管理，如在ONOS或OpenDaylight这样的SDN控制器中。

请注意，要实现具体的Meter配置，你需要具体的P4Runtime库以及相关软件来和交换机通信。Meter的确切配置细节将依赖于你的P4程序和交换机的能力。

上述内容提供概念性的解释，实际的实现涉及复杂的网络编程，并在实际部署中需要适当的安全措施（如对gRPC通信的认证和加密）。



### **`v1model.p4`** 中关于`meter`的阐述
````C
extern meter
#if V1MODEL_VERSION >= 20200408
<I>
#endif
{
    /***
     * A meter object is created by calling its constructor.  This
     * creates an array of meter states, with the number of meter
     * states specified by the size parameter.  The array indices are
     * in the range [0, size-1].  For example, if in your system you
     * have 128 different "flows" numbered from 0 up to 127, and you
     * want to meter each of those flows independently of each other,
     * you could do so by creating a meter object with size=128.
     *
     * You must provide a choice of whether to meter based on the
     * number of packets, regardless of their size
     * (MeterType.packets), or based upon the number of bytes the
     * packets contain (MeterType.bytes).
     */
    meter(bit<32> size, MeterType type);
    // FIXME -- size arg should be `int` but that breaks typechecking

    /***
     * execute_meter() causes the meter state with the specified index
     * to be read, modified, and written back, atomically relative to
     * the processing of other packets, and an integer encoding of one
     * of the colors green, yellow, or red to be written to the result
     * out parameter.
     *
     * @param index The index of the meter state in the array to be
     *              updated, normally a value in the range [0,
     *              size-1].  If index >= size, no meter state will be
     *              updated.
     * @param result Type T must be bit<W> with W >= 2.  When index is
     *              in range, the value of result will be assigned 0
     *              for color GREEN, 1 for color YELLOW, and 2 for
     *              color RED (see RFC 2697 and RFC 2698 for the
     *              meaning of these colors).  When index is out of
     *              range, the final value of result is not specified,
     *              and should be ignored by the caller.
     */
#if V1MODEL_VERSION >= 20200408
    void execute_meter<T>(in I index, out T result);
#else
    void execute_meter<T>(in bit<32> index, out T result);
#endif
}

extern direct_meter<T> {
    /***
     * A direct_meter object is created by calling its constructor.
     * You must provide a choice of whether to meter based on the
     * number of packets, regardless of their size
     * (MeterType.packets), or based upon the number of bytes the
     * packets contain (MeterType.bytes).  After constructing the
     * object, you can associate it with at most one table, by adding
     * the following table property to the definition of that table:
     *
     *     meters = <object_name>;
     */
    direct_meter(MeterType type);
    /***
     * After a direct_meter object has been associated with a table as
     * described in the documentation for the direct_meter
     * constructor, every time the table is applied and a table entry
     * is matched, the meter state associated with the matching entry
     * is read, modified, and written back, atomically relative to the
     * processing of other packets, regardless of whether the read()
     * method is called in the body of that action.
     *
     * read() may only be called within an action executed as a result
     * of matching a table entry, of a table that has a direct_meter
     * associated with it.  Calling read() causes an integer encoding
     * of one of the colors green, yellow, or red to be written to the
     * result out parameter.
     *
     * @param result Type T must be bit<W> with W >= 2.  The value of
     *              result will be assigned 0 for color GREEN, 1 for
     *              color YELLOW, and 2 for color RED (see RFC 2697
     *              and RFC 2698 for the meaning of these colors).
     */
    void read(out T result);
}
````