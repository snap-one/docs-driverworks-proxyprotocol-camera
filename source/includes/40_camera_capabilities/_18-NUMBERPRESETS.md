## number\_presets
Defines the number of presets displayed on the UI. See the [Camera Proxy and Presets][1] documentation for more information.


### Signature

`<number_presets></number_preets>`


| Parameter | Description       |
| --------- | ----------------- |
| num       | Number of Presets |


### Usage Note

Default is 0. Note that Navigator devices will truncate this value to a supported number.


### Example

```xml
<capabilities>
    <number_presets>6</number_presets>
</capabilities>
```



[1]:	https://snap-one.github.io/docs-driverworks-fundamentals/#proxy-specific-information-camera-proxy-and-presets