# BEM CSS

Tài liệu sau đây cung cấp cái nhìn tổng quan về cách sử dụng [BEM CSS](https://codetot.net/toan-tap-ve-bem-css/) và ứng dụng trong các project thực tế. Các quy ước này nên được xem xét nghiêm túc trong các project thực tế.

Bạn có thể xem bài viết gốc về BEM CSS tại [Code Tốt](https://codetot.net/toan-tap-ve-bem-css/)

## 1. Cấp độ quan hệ cha - con giữa các element (module)

Sử dụng cấp độ này với các mục tiêu sau:

**📌Tất cả element nằm trong module thì đều có prefix của module**

```
.hero
.hero__title
.hero__container
.hero__description
.hero__button-text
.hero__button-link
```

## 2. Cấp độ ngang hàng

Thực hiện cấp độ ngang hàng trong các trường hợp sau:

### 2.1. Module có sự trùng khớp về markup, nhưng khác style

📌 Được hiểu là: không cần sắp xếp lại thứ tự element, có thể thừa/thiếu element do người dùng không nhập, không thay đổi layout bên trong/bên ngoài các element.

Chỉ được lựa chọn 1 trong 2 phương án sau trên mỗi project:

**Phương án 1: Đặt tên theo trang có chứa module:**

> Trường hợp này, module chỉ nên có duy nhất class extend, ví dụ `two-up two-up--about` mà thôi. Không nên có các class BEM theo chức năng nữa.

```
.two-up--about // Two Up trên page About
.two-up--related // Two Up có chứa Related posts nằm ở single template
```

**Phương án 2: Đặt tên theo chức năng module:**

> Trường hợp này module có thể chứa nhiều class, ví dụ `two-up two-up--bg two-up--bottom-border` nhưng không có class nào theo trang nữa

```
.two-up--bg-blue // Two Up giống mặc định, chỉ set nền màu `blue`
.two-up--bottom-border // Two Up giống mặc định, có thêm border ở phía dưới
```

❗️Các trường hợp developer thường nhầm lẫn:

Ví dụ:

Class đổ màu nhưng lại làm cả nhiệm vụ ẩn button là không hợp lý.

```
.card-news--bg-gray {
  .card-news__button { display: none; }
}
```

### 2.2. Trong cùng module có nhiều element ngang hàng có chung 1 phần style

📌Thường quy ước phần này liên quan nhiều tới layout, ví dụ cấu trúc cột như ví dụ dưới đây.

Ví dụ:

Ta có 2 class sau:

```
<div class="two-up__block two-up__block--left"></div>
<div class="two-up__block two-up__block--right"></div>
```

Và style:

```
.two-up__block {
  width: 100%;
  padding: 0 20px;
}
.two-up__block--left {
  @media (--m) {
    width: 60%;
  }
}
.two-up__block--right {
  @media (--m) {
    width: 40%;
  }
}
```

❗️Trường hợp developer thường nhầm lẫn:

```
.two-up__headline // Cân trái
.two-up__headline--center // Cân giữa
```

> Các mối quan hệ như thế này nên chuyển về class module extends để xử lý.

```
.two-up--headline-center {
  .two-up__headline {
    text-align: center;
  }
}
```

Lý do: Không thể check điều kiện và gán class cụ thể cho từng element bên trong module vì sẽ làm rối logic.

Ví dụ markup nếu có viết theo kiểu này sẽ bị rối như sau:

Truyền vào:

```
the_module('two-up', array(
  'headline_class' => 'two-up__headline--center',
  'description_class' => 'two-up__description--center'
));
```

Code module:

```
<div class="two-up<?php if( !empty($class) ) : echo ' ' . $class; endif; ?>">
  ...
  <h2 class="two-up__headline<?php if( !empty($headline_class) ) : echo ' ' . $headline_class; endif; ?>"><?php echo $headline; ?></h2>
  ...
</div>
```

## Sử dụng BEM kết hợp global class

### Global class là gì?

Là những class chung được sử dụng với mục đích cho những layout/typography chung, có mức độ tái sử dụng mạnh và không phụ thuộc vào quan hệ module.

```
.grid {
  display: flex;
  flex-wrap: wrap;
  margin: 0 -20px;
}
.grid__item {
  padding: 0 20px;
}
```
Ví dụ như với class ở trên, nhiệm vụ của nó là tạo layout cột. Nhiệm vụ của dev là add `width` vào các breakpoint là nó sẽ hoạt động.

### Sử dụng kết hợp BEM

Trong markup, thứ tự ưu tiên là `global class` sau đó viết `module class` để override khi cần.

```
<h2 class="h2 callout__headline"></h2>
```

Viết theo kiểu composit thì sẽ có nhiều class global hơn, ví dụ:

```
<h2 class="h2 underline bold callout__headline"></h2>
```

❗️Developer không nesting class (ghi bằng class cha) như ví dụ sau:

```
/** two-up.css **/
.two-up {}
.two-up__figure {}
.grid__item figure {}
```

## Kết luận

Việc sử dụng nguyên tắc BEM cần nắm được tại sao và lựa chọn phương án xử lý theo các quy ước trên sẽ giảm thời gian code thừa.
Nếu quan sát tỷ lệ code phải override lại lớn thì chủ động tách module và markup từ đầu, đặt tên module mới.
