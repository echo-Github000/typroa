### vlink与模组交互的中间件解析

#### 1. vlink -> zigbee module

```
vlink_get_active_cmd -> pm_uart_send_cmd -> write(uart_handle->tx_pipe_write_fd) -> tx_task(uart) -> read(uart_handle->tx_pipe_read_fd) -> sendUartData -> safe_write -> write(fd = "/dev/ttyS1")
```

uart_handle为"uart"的handle，/dev/ttyS1为vlink与ZigBee模组通讯的串口



#### 2. zigbee module -> vlink

```
read(fd = "/dev/ttyS1") -> safe_read -> uart_read_select -> rx_task(uart) -> recvUartPackHandle -> parse_patterned_pack -> copyCmd2Cache -> ListAppend(uart_handle->cmd_cache_list) -> ListDetachNodeById(uart_handle->cmd_cache_list) -> readCmdData -> pm_uart_recv_cmd -> vlink_dev_parse_cmd

```



#### 3. vlink -> mesh module

```c
vlink_get_active_cmd -> pm_pipe_send_cmd -> tx_task(pipe) -> read(uart_handle->tx_pipe_read_fd) -> sendUartData -> safe_write -> write(dev_tx_write_fd) -> cli_process -> emberProcessCommandString -> emberSerialReadByte(dev_rx_read_fd) -> callCommandAction -> emberCommandActionHandler -> emberCommandTable[] -> emberAfCustomCommands[] -> xxx_handle -> nrf_mesh_packet_send -> nrf_serial_tx -> uart_msg_enqueue -> uart_msg_dequeue -> uart_drv_send -> write("/dev/ttyS2")
```

这里有一点需要特别注意的是：在调用nrf_serial_tx将指令发送到缓冲区的时候，即尚未等到该指令回复就调用了MESH_TO_WIFI_CMD将期望结果返回给了vlink，调用流程为：
MESH_TO_WIFI_CMD -> pipe_data_send -> pm_pipe_recv_cmd -> vlink_dev_parse_cmd


添加新的发送消息时应留意**emberAfCustomCommands[]**，向其中填充对应的方法和handle即可。



#### 4. mesh module -> vlink

```c
read("/dev/ttyS2") -> uart_drv_receive -> uart_msg_packet_in -> uart_msg_enqueue(rx) -> uart_msg_dequeue(rx) -> serial_handler_common_rx -> m_cmd_handlers[](Serial) ->xxx_handle(handle_cmd_mesh_message_received_unicast)
-> m_cmd_handlers[](event_status) -> xxx_handle -> MESH_TO_WIFI_CMD -> pipe_data_send -> rx_task(pipe) -> uart_read_select -> recvUartPackHandle -> parse_patterned_pack -> copyCmd2Cache -> ListAppend(uart_handle->cmd_cache_list) -> ListDetachNodeById(uart_handle->cmd_cache_list) -> readCmdData -> pm_pipe_recv_cmd -> vlink_dev_parse_cmd
```

添加新的接收消息应留意两组serial_handler_common_opcode_to_fp_map_t **m_cmd_handlers[]**，分别位于serial_callback.c和serial_process.c，添加串口通讯的opcode和对应处理handle。 留意event_status_handler **m_cmd_handlers[]**，添加mesh协议中的opcode和对应handler。

