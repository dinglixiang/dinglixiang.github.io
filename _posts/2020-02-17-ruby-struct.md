---
title: "Ruby: use Struct for structed data convertion"
layout: post
date: 2020-02-17 23:02
guid: urn:uuid:f89951c5-691f-46c1-b699-907b09031e77
author: "Lixiang"
tags:
  - Ruby
---

最近实现业务时，遇到了数据需要双向同步的场景，两边的数据格式完全不一致。

e.g.

The old one

```json
{
  "type": "Video",
  "controls": "controls",
  "vid": "m0909jcjxxx",
  "poster": "https://static-assets.xxx.com/images/default-poster-image.png",
  "video_autoplay": true,
  "category": "cloudDemand",
  "src": "https://v.qq.com/x/page/m0909jcjxxx.html",
  "error_message": ""
}
```

The new one

```json
{
  "id": "b7a3194d-3b1e-4538-b4e8-c8ee84d16dc2",
  "type": "video",
  "settings": {
    "cloud_demand": {
      "vid": "m0909jcjxxx",
      "url": "https://v.qq.com/x/page/m0909jcjxxx.html",
      "poster": "https://static-assets.xxx.com/images/default-poster-image.png",
      "auto_play": true,
      "controls": "controls",
      "error_message": ""
    },
    "video_link": {
      "vid": "",
      "url": "",
      "poster": "",
      "auto_play": true,
      "controls": "",
      "error_message": ""
    },
    "video_type": "cloudDemand"
  },
  "values": {}
}
```

针对里面object，同义但不同key，两边都遵循自己的数据结构, 数据格式处理时比较麻烦。

针对这个场景，`Struct`能够很好的处理。

```ruby
# 老数据结构
OldVideo = Struct.new(:vid, :src, :poster, :video_autoplay, :controls) do
  def to_newest
    {
      vid: vid || '',
      url: src || '',
      poster: poster || '',
      auto_play: video_autoplay || true,
      controls: controls || '',
      error_message: ''
    }
  end
end

# 新数据结构
NewVideo = Struct.new(:vid, :url, :auto_play, :poster, :controls, :error_message) do
  def to_oldest
    {
      vid: vid || '',
      src: url || '',
      poster: poster || '',
      video_autoplay: auto_play || true,
      controls: controls || '',
      error_message: error_message || ''
    }
  end
end

# 假设data为上述对应的video数据

old_one = OldVideo.new(data['vid'],
                       data['src'],
                       data['poster'],
                       data['video_autoplay'],
                       data['controls'])
old_one.to_newest

# 对于NewVideo，同理可得
new_one = NewVideo.new(info['vid'],
                       info['url'],
                       info['auto_play'],
                       info['poster'],
                       info['controls'],
                       info['error_message'])
new_one.to_oldest

# 针对VideoLink为空的类型，也可以通过OldVideo.new.to_newest的方式获取空的structed data.
# 或者在Struct中重新定义新的方法设置默认值
```

FYI:
- [ruby struct](https://www.leighhalliday.com/ruby-struct)
- [ruby struct and openstruct](https://www.rubyguides.com/2017/06/ruby-struct-and-openstruct/)
- [struct in ruby](https://medium.com/rubycademy/struct-in-ruby-cac1c7514517)
