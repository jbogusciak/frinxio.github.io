# Databse JSONB Filtering

!!!
The example of using the jsonb-filter query parameter: **parent-path?jsonb-filter=expression**

PostgreSQL documentation: [JSON Functions and Operators](https://www.postgresql.org/docs/13/functions-json.html)
!!!

#### Jsonb-filter expression

The base expression must contain **path**, **operator** and **value**.
The jsonb-filter can contain one or more expressions joined with AND
(&&) or OR (||) operator. if the && operator is used it must be encoded.

**Path**

The path to the data that the users want to filter. The path can be:

1.  **Relative path**

In this case, the path must be prefixed with <**@**>. This path is relative to the **parent-path**

```
{@/description}
```

2.  **Absolute path**

In this case, a path must be prefixed with **\$**. This path must start with a top-level parent container

```
{$/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration=act,Bundle-Ether1/description}
```

!!!
Sometimes especially absolute paths can contain a key of some item
with special characters. In this case it is necessary wrap this key in
a special syntax **(\#example-key-name)** and also encode these
wrapping symbols - **%28%23example-key-name%29**. If the key is a
composite key, it is necessary to wrap the whole key with these
symbols. If the user is not sure if the path contains special
characters, it is always recommended to use this special syntax.
!!!

**Single key:**

{\$/frinx-openconfig-interfaces:interfaces/interface=%28%23MgmtEth0/RP0/CPU0/0%29}

**Composite key:**

{\$/Cisco-IOS-XR-ifmgr-cfg:interface-configurations/interface-configuration=%28%23act,GigabitEthernet0/0/0/2%29}

**Operator**

When the path is constructed then the user can use one of the operators
in the table below

| Value/Predicate Description | Description |
| --- | --- |
| == | Equality operator |
| != | Non-equality operator |
| \<\> | Non-equality operator (same as !=) |
| \< | Less-than operator |
| \<= | Less-than-or-equal-to operator |
| \> | Greater-than operator |
| \>= | Greater-than-or-equal-to operator |
| true | Value used to perform a comparison with JSON true literal |
| false | Value used to perform a comparison with JSON false literal |
| null | Value used to perform a comparison with JSON null value |
| && | Boolean AND |
| \|\| | Boolean OR |
| ! | Boolean NOT |
| like\_regex | Tests whether the first operand matches the regular expression given by the second operand |
| starts with | Equality operator |
| exists | Equality operator |
| is unknown | Equality operator |


**Value**

The last element of the jsonb-filter expression is a value based on
which the user wants to filter the data.

#### Jsonb-filter examples

**1. Examples of using the relative paths in the jsonb-filter**

Example of filtering the list of interfaces based on the enabled
parameter where the **equality operator** is used as the operator

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter={@/config/enabled} == true&content=nonconfig
```

Example of filtering the list of interfaces based on the mtu parameter
where the **less-than** is used as the operator

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter={@/config/mtu} < 1600&content=nonconfig
```

Example of filtering the list of interfaces based on the name parameter
where the **like\_regex** is used as the operator

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter={@/config/name} like_regex "Bundle.*"&content=nonconfig
```

Example of filtering the list of interfaces where a combination of
expressions is used

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter=({@/config/name} != "Bundle-Ether1" %26%26 {@/config/name} starts with "Bundle-Ether") || ({@/config/name} like_regex "Gigabit.*" %26%26 {@/config/enabled} == true)&content=nonconfig
```

Example of filtering the list of interfaces where the **exists**
operator is used

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface?jsonb-filter=(exists({@/subinterfaces}))&content=nonconfig
```

**2. Example of using the absolute path in the jsonb-filter**

Example of filtering the list of interfaces based on the name parameter
where **equality operator** is used as the operator. Interface name
"GigabitEthernet0/0/0/2" is a key value that contains slashes. For this
reason, it is necessary to wrap this key into wrapping symbols
(\#GigabitEthernet0/0/0/) and also encode these symbols
%28%23GigabitEthernet0/0/0/2%29.

```
http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=IOSXR/configuration/frinx-openconfig-interfaces:interfaces/interface=GigabitEthernet0%2F0%2F0%2F2?jsonb-filter={$/frinx-openconfig-interfaces:interfaces/interface=%28%23GigabitEthernet0/0/0/2%29/config/enabled}==false&content=nonconfig
```
