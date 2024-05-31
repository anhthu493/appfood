# appfood



create database food;
use food;

create table userr (
    userid int primary key identity(1,1),
    full_name varchar(50),
    email varchar(50),
    password varchar(50)
);

create table restaurant (
    res_id int primary key identity(1,1),
    res_name varchar(50),
    image varchar(200),
    descc varchar(200)
);

create table food (
    food_id int primary key identity(1,1),
    food_name varchar(50),
    image varchar(200),
    price float,
    descc varchar(200),
    type_id int
);

create table food_type (
    type_id int primary key identity(1,1),
    type_name varchar(50)
);

create table orderr (
    order_id int primary key identity(1,1),
    userid int,
    food_id int,
    amount int,
    code varchar(50),
    arr_sub_id varchar(50),
    foreign key (userid) references userr(userid),
    foreign key (food_id) references food(food_id)
);

create table like_res (
    userid int,
    res_id int,
    like_date datetime,
    primary key (userid, res_id),
    foreign key (userid) references userr(userid),
    foreign key (res_id) references restaurant(res_id)
);

create table rate_res (
    userid int,
    res_id int,
    amount int,
    rate_date datetime,
    primary key (userid, res_id),
    foreign key (userid) references userr(userid),
    foreign key (res_id) references restaurant(res_id)
);

-- chèn dữ liệu mẫu cho userr
insert into userr (full_name, email, password) values 
('Nguyen A', '1@gmail.com', 'aa11'),
('Tran B', '2@gmail.com', 'aa22'),
('Nguyen C', '3@gmail.com', 'aa33'),
('Tran D', '4@gmail.com', 'aa44'),
('Nguyen E', '5@gmail.com', 'aa55');

-- chèn dữ liệu mẫu cho restaurant
insert into restaurant (res_name, image, descc) values 
('com hen', 'com1.jpg', 'ngon'),
('com dau', 'com2.jpg', 'ngon'),
('cơm chay', 'com3.jpg', 'ngon');

-- chèn dữ liệu mẫu cho food_type
insert into food_type (type_name) values 
('an vat'),
('an chay');

-- chèn dữ liệu mẫu cho food
insert into food (food_name, image, price, descc, type_id) values 
('com hen', 'com1.jpg', 10000, 'com hen', 1),
('com dau', 'com2.jpg', 20000, 'com dau', 1),
('com chay', 'com3.jpg', 30000, ' com chay', 2);

-- chèn dữ liệu mẫu cho orderr
insert into orderr (userid, food_id, amount, code, arr_sub_id) values 
(1, 1, 2, 'code1', 'sub1'),
(2, 2, 1, 'code2', 'sub2'),
(1, 3, 1, 'code3', 'sub3');

-- chèn dữ liệu mẫu cho like_res
insert into like_res (userid, res_id, like_date) values 
(1, 1, '2021-01-01 01:00:00'),
(1, 2, '2022-02-02 01:00:00'),
(2, 1, '2021-03-03 02:00:00'),
(3, 1, '2023-04-04 03:00:00'),
(4, 2, '2022-05-05 04:00:00'),
(5, 3, '2024-06-06 05:00:00');

-- chèn dữ liệu mẫu cho rate_res
insert into rate_res (userid, res_id, amount, rate_date) values 
(1, 1, 5, '2021-01-01 10:00:00'),
(2, 1, 4, '2022-02-02 11:00:00'),
(3, 2, 3, '2023-03-03 12:00:00'),
(4, 3, 2, '2024-06-04 13:00:00');

-- tìm 5 người đã like nhà hàng nhiều nhất
select userid, count(res_id) as like_count
from like_res
group by userid
order by like_count desc
offset 0 rows fetch next 5 rows only;

-- tìm 2 nhà hàng có lượt like nhiều nhất
select res_id, count(userid) as like_count
from like_res
group by res_id
order by like_count desc
offset 0 rows fetch next 2 rows only;

-- tìm người đã đặt hàng nhiều nhất
select userid, count(order_id) as order_count
from orderr
group by userid
order by order_count desc
offset 0 rows fetch next 1 rows only;

-- tìm người dùng không hoạt động trong hệ thống
select u.userid, u.full_name
from userr u
left join orderr o on u.userid = o.userid
left join like_res lr on u.userid = lr.userid
left join rate_res rr on u.userid = rr.userid
where o.userid is null 
  and lr.userid is null 
  and rr.userid is null;

-- tìm thông tin người dùng không hoạt động
select u.userid, u.full_name, u.email
from userr u
left join orderr as o on u.userid = o.userid
left join like_res lr on u.userid = lr.userid
left join rate_res rr on u.userid = rr.userid
where o.order_id is null and lr.res_id is null and rr.res_id is null;
