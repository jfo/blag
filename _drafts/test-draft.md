---
title:  "draft test"
layout: base
---

hi

```rb
Dir["./*"].map {|a|
    cand = File.open(a).grep(/date\:/).first
    next if cand.include? "UTC"

    date = cand.tr("date\:", '')
    .tr("^[0-9]\/\-", '')
    .gsub(/..\:.+/, '')
    .tr("\/", '-')
    .split('-').map{|e| e.rjust(2, padstr='0')}
    .join '-'

    p date + '-' + a[2..-1]
    File.rename(a[2..-1], date + '-' + a[2..-1])
    gets
}
```
