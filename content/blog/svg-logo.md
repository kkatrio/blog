---
title: "svg logo"
date: 2019-08-26T15:45:41+03:00
---

#### inkscape: save as optimized
mind any font to be path actually

#### keep only the xmlns and version tags, remove width, height
vim can't handle it, try atom

#### base64 encode
```
cat sh-prod.svg | openssl base64 |  tr -d '\n' | xclip
```

#### update css
update width and height if necessary. It's svg!
```
.logo {
  width: 126px;
  height: 76px;
  background: url("data:image/svg+xml;base64,[data]") no-repeat center center;
}
```
