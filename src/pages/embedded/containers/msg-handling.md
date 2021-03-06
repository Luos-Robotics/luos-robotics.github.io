
# Containers communication handling messages
> **Warning:** Make sure to read and understand how to [Create Luos containers](./create-project.md) before reading this page.

As a developer, you will have to create and use Luos messages to exchange information between <span class="cust_tooltip">containers<span class="cust_tooltiptext">{{container_def}}</span></span>. In order to do that, you have to understand how messages work.

## Message structure

Luos messages are managed by the `msg_t` structure:

```C
typedef struct{
    header_t header;
    uint8_t data[MAX_DATA_MSG_SIZE];
}msg_t;
```

All messages have a header. A header is a 7-byte field containing all information allowing containers to understand messages context. All containers on the network catch and decode the header of each sent and received message.

`data` is a table containing data.

> **Info:** MAX_DATA_MSG_SIZE represenst the maximum size of messages (default value is 128 bytes);

## Header
To send data to any containers you want, you will have to first fill out some information on the header.

here is the `header_t` structure:
```C
typedef struct{
    uint16_t protocol : 4;    /*!< Protocol version. */
    uint16_t target : 12;     /*!< Target address, it can be (ID, Multicast/Broadcast, Type). */
    uint16_t target_mode : 4; /*!< Select targeting mode (ID, ID+ACK, Multicast/Broadcast, Type). */
    uint16_t source : 12;     /*!< Source address, it can be (ID, Multicast/Broadcast, Type). */
    uint8_t cmd;              /*!< msg definition. */
    uint16_t size;            /*!< Size of the data field. */
}header_t;
```

- **Protocol (4 bits)**: This field provides the protocol revision. This field is automatically filled, you don't have to deal with it.
- **Target (12 bits)**: This field contains the target address. Make sure to understand the real destination of this field, you have to know the addressing mode contained on the *Target_mode* field.
- **Target_mode (4 bits)**: This field indicates the addressing mode and how to understand the *Target* field. It can take different values:
  - **ID**: This mode allows to communicate with a unique container using its ID **without** acknowledgment return.
  - **ID_ACK**: This mode allows to communicate with a unique container using its ID **with** acknowledgment return.
  - **Multicast/Broadcast**: This mode allows multiple containers to catch a message. In this case, the message contains a type of data used by multiple containers.
  - **Type**: This mode sends a message to all containers with a given type, for example all "Sharp digital distance sensor".
- **Source (12 bits)**: The unique ID of the transmitter container.
- **CMD (8 bits)**: The command defines the transmitted data's type.
- **Size (16 bits)**: Size of the incoming data.

# Receive and send a basic message
To send a message you have to:
 1) Create a message variable
 2) Set the **target_mode**
 3) Set the **target**
 4) Set the **cmd**
 5) Set your data **size**
 6) Set your data
 7) Send it.

Below is a basic reply example that you can find in a container reception callback. For more information on handling a message received, see [message handling configuration](./msg-handling.html#message-handling-configurations) page.
```c
void containers_MsgHandler(container_t *container, msg_t *msg) {
    if (msg->header.cmd == ASK_PUB_CMD) {
        // fill the message info
        msg_t pub_msg;
        pub_msg.header.target_mode = ID;
        pub_msg.header.target = msg->header.source;
        pub_msg.header.cmd = IO_STATE;
        pub_msg.header.size = sizeof(char);
        pub_msg.data[0] = 0x01;
        Luos_SendMsg(container, &pub_msg);
        return;
    }
}
```
> **Note:** the function _Luos_SendMsg_ return an error_status that informw user if the message was stored in buffer and is ready to be sent. User can monitor the return value and make a blocking statement to be sure that the message is ready to be sent.

```c
while(Luos_SendMsg(container, &pub_msg) ! SUCCEED);
```

## Container exclusion
Luos includes an acknowledgement management using the **ID_ACK** target_mode. This mode guaranties the proper reception of critical messages.

If Luos fails to reach its target using ID_ACK, it will retry, sending up to 10 times. If the acknowledgement still fails, the targeted container is declared excluded. Excluded containers are removed from the routing table to avoid any messaging by any containers, preserving bandwidth for the rest of the system.

## Large data
You will sometimes have to deal with large data that could be larger than the maximum 128-byte data on a Luos message. Fortunately, Luos is able to automatically fragment and de-fragment the data above this side. To do that, you will have to use another send function that will take care of setting the messages' size, and the data fields.

For example, here is how to send a picture:
```c
// fill the large message info
msg_t msg;
color_t picture[300*300] = {/*Your favorite cat picture data*/};
msg.header.target_mode = ID_ACK;
msg.header.target = 12;
msg.header.cmd = COLOR;
Luos_SendData(container, &msg, picture, sizeof(color_t)*300*300);
return;
```

In the reception callback, here is the code for retrieve the message with the receiving container (the one with ID 12):
```c
color_t picture[300*300];
void containers_MsgHandler(container_t *container, msg_t *msg) {
    if (msg->header.cmd == COLOR) {
        Luos_ReceiveData(container, msg, (void*)picture);
    }
}
```

> **Note:** If you have to deal with high-frequency real-time data, please read [the Streaming page](./streaming.md).

## Time-triggered update messages
Luos provides a standard command to ask a container to retrieve values from a sensor, called `ASK_PUB_CMD`. However, sometimes apps need to poll values from sensors, but the act of repeatedly retriving a value using the `ASK_PUB_CMD` command may result in the use of a lot bandwidth and take up valuable resources.
In this kind of polling situation, **you can use the time-triggered auto-update features available from any Luos container**. This feature allows you to ask a container to send you an update of any value each X milliseconds.
To use it, you have to setup targeted container with a message containing a standard time <span class="cust_tooltip">object dictionary<span class="cust_tooltiptext">{{od_def}}</span></span>, but with a specific command associated to it.

For example, to update a container each 10 ms:
```C
time_luos_t time = TimeOD_TimeFrom_ms(10);
msg_t msg;
msg.header.target = id;
msg.header.target_mode = IDACK;
TimeOD_TimeToMsg(&time, &msg);
msg.header.cmd = UPDATE_PUB;
Luos_SendMsg(app, &msg);
```

> **Info:** containers can handle only one time-triggered target, 2 containers of the same network can't ask a time-triggered value from the same container.

> **Warning:** To prevent any ID movement, auto-update configuration is reset on all containers on each detection (see [Routing table page](./routing-table.md) for more information).

# Message Handling configurations

Message callbacks of containers can be really difficult to use when a project has high real-time constraints.<br/>
Luos provides two different configurations allowing you to choose the best way for you to deal with messages.
The message handling configuration is set during the [initialization of a container](./create-containers.md).

|Configuration|execution type|
|:---:|:---:|
|[Callback (default)](#Callback-configuration)|runtime callback|
|[Polling](#polling-configuration)|no callback|

The following sections detail how the different configurations work.

## Callback configuration
This configuration is the default and most common setup. In this configuration, Luos directly calls the container callback during runtime. The time between the physical reception of a message and the callback may vary depending on the `luos_loop()` function execution frequency.<br/>
With this configuration, you have no real constraints on the callback's time of execution, you can reply to a message directly on the callback.

To setup this configuration you have to simply setup the callback at container creation.

Here is a code example with a button:
```c
void Button_MsgHandler(container_t *container, msg_t *msg) {
    if (msg->header.cmd == ASK_PUB_CMD) {
        // The message is filled with global variable with proper data
        msg_t pub_msg;
        pub_msg.header.cmd = IO_STATE;
        pub_msg.header.target_mode = ID;
        pub_msg.header.target = msg->header.source;
        pub_msg.header.size = sizeof(char);
        pub_msg.data[0] = HAL_GPIO_ReadPin(BTN_GPIO_Port, BTN_Pin);
        // Sending the message
        Luos_SendMsg(container, &pub_msg);
        return;
    }
}

void Button_Init(void) {
    // container creation: (callback, container type, Default alias)
    container_t* container = Luos_CreateContainer(Button_MsgHandler, STATE_MOD, "button_mod");
}

void Button_Loop(void) {
}
```

## Polling configuration
This configuration is often used in Arduino libraries to receive information in a basic way. This method allows you handle messages only when the user wants to do it in the loop of the container.

To setup this configuration, you have to create your container without any callbacks.

See the following code as an example, with a button:

```c
container_t* container;
void Button_Init(void) {
    container = Luos_CreateContainer(0, STATE_MOD, "button_mod");
}

void Button_Loop(void) {
    if (Luos_NbrAvailableMsg()) {
        msg_t *msg = Luos_ReadMsg(container);
        if (msg->header.cmd == ASK_PUB_CMD) {
            // The message is filled with global variable with proper data
            msg_t pub_msg;
            pub_msg.header.cmd = IO_STATE;
            pub_msg.header.target_mode = ID;
            pub_msg.header.target = msg->header.source;
            pub_msg.header.size = sizeof(char);
            pub_msg.data[0] = HAL_GPIO_ReadPin(BTN_GPIO_Port, BTN_Pin);
            // Sending the message
            Luos_SendMsg(container, &pub_msg);
        }
    }
}
```
