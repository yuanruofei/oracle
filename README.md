# oracle
oracle数据库
1 别名
Select salary*12 as “年薪” from hr.employees;
2 字符串连接
把姓和名分别显示在不同的字段中
select last_name,first_name  from hr.employees

把姓和名连接在一起，并且显示在同一个字段中
select last_name || first_name  姓名 from hr.employees

姓和名之间加上空格
select last_name || ' ' || first_name 姓名 from hr.employees
3 消除重复行
查询所有人的部门id,有的员工在同一个部门，所以会看到重复的部门id号
select department_id from hr.employees

消除重复的部门id号
Select distinct department_id from hr.employees
4 条件限定
比较
只查询工资高于5000的员工
select * from hr.employees where salary >5000

between and
查询工资在3000到5000之间的闭区间，即取得到3000
select * from hr.employees where salary between 3000 and 5000

in
即薪水要么等于3000要么等于6000
select * from hr.employees where salary in(3000,6000)

like
名字里倒数第二个字母是a的员工 %表示任意匹配，有或者没有都可以 _表示有，并且只有一个
select * from hr.employees where first_name like '%a_'

is null 
查询所在部门为null的员工,即该员工暂时还未分配工作部门
select * from hr.employees where department_id is null
5 逻辑条件
与
使用between
select * from hr.employees where salary between 3000 and 5000
使用and 效果是一样的
select * from hr.employees where salary>= 3000 and salary<=5000

或
要么满足正数第二个是a，要么满足倒数第二个是a
select * from hr.employees where first_name like '%a_' or first_name like '_a%'

非
排除姓名里倒数第二个字母是a的员工
select * from hr.employees where first_name not like '%a_'
6 阶段练习1
1查询所有员工的姓名（last_name+' '+first_name）,工资，年终奖金（工资的百分之八十 乘以 commission_pct 在加500）别名（年终奖）。
  
select e.last_name|| ' ' || e.first_name, e.salary,e.salary*0.8*e.commission_pct+500 as 年终奖 from hr.employees e
  
2查询所有有人员的部门编号，并且去掉重复行。
select distinct e.department_id from hr.employees e
  
3查询员工的姓名，工资，岗位(JOB)，要求工资为2000~3000并且JOB以‘ERK’结束
  
select * from hr.employees e where e.salary between 2000 and 3000 and e.job_id like '%ERK'
  
4查询所有有人员的岗位编号，要求岗位(JOB)中包含‘L’同时岗位(JOB)名称以‘N’或‘K’结束，去掉重复行。
  
select distinct e.job_id from hr.employees e where e.job_id like '%L%' and ( e.job_id like '%N' or e.job_id like '%K' )
7 排序查询
根据工资倒排序
select * from hr.employees order by salary desc
8 关联查询
查询员工姓名以及员工所在部门的名称

select e.first_name,e.department_id from hr.employees e
把员工名字和部门id查询出来了
其中e是表别名
但是部门名称在hr.employees表里没有
要知道部门名称必须查询hr.departments 这张表

select d.department_id,d.department_name from hr.departments d
把部门id和部门名称查询出来了

但是员工名字和部门名称在不同的表的，为了一次性把两个数据都查询出来，需要对两张表进行关联
select e.first_name,d.department_name from hr.employees e
left join hr.departments d
on e.department_id = d.department_id
left join 是进行表关联
on 指定关联字段
9 统计函数
统计月薪高于5000的有多少人
select count(*) from hr.employees e where e.salary >5000
100部门薪水最高最低是多少
select max(salary) ,min(salary)  from hr.employees e where e.department_id = 100
平均薪资
select avg(salary) from hr.employees e
10 分组查询
按照部门进行分组，查看每个部门的平均薪资是多少
select avg(salary),e.department_id  from hr.employees e
group by e.department_id

having
以部门进行分组，找出部门平均薪资高于5000的数据
select avg(salary),e.department_id  from hr.employees e
group by e.department_id 
having avg(salary) >5000
11 阶段练习2
查询部门编号大于等于50小于等于90的部门中工资小于5000的员工的编号、部门编号和工资
select e.employee_id, e.department_id,e.salary from hr.employees e where e.department_id between 50 and 90 and e.salary < 5000

显示员工姓名加起来一共15个字符的员工
select * from hr.employees e where e.first_name||e.last_name like '_______________';

显示示不带有“ R ”的员工的姓名
select * from hr.employees e where e.first_name||e.last_name not like '%R%';

查询有员工的部门的平均工资，要求显示部门编号，部门名，部门所在地（需要多表关联查询： employees, department, location）
select avg(e.salary), e.department_id,d.department_name,l.street_address from hr.employees e left join hr.departments d on e.department_id = d.department_id left join hr.locations l on d.location_id = l.location_id group by e.department_id ,d.department_name,l.street_address
12 子查询
子查询即可以简单的理解成同时执行多条sql语句 
将一条sql语句的查询结果作为条件来执行另一条sql语句

查询出Bruce的月薪，得到数据是6000
select e.salary from hr.employees e where e.first_name= 'Bruce'

统计月薪高于6000的人数
select count(*) from hr.employees e where e.salary > 6000.00
这需要两句sql语句
通过子查询，一句sql语句就足够了
select count(*) from hr.employees e where e.salary > 
(
 select e.salary from hr.employees e where e.first_name= 'Bruce'
)
小括号里的sql即为子查询
13 分页查询
只查出5条数据
select * from hr.employees e where rownum<=5
查出薪水最高的5-10条数据
select * from
(select rownum r, e1.* from
  (
   select e.* from hr.employees e  order by e.salary desc
  ) e1
) e2 where e2.r >=5 and e2.r<=10
14 阶段练习3
--查询所在部门所在城市为'South San Francisco'的员工
select e.last_name||' '||e.first_name as name,d.department_id,l.location_id,l.city
from employees e,departments d,locations l
where e.department_id=d.department_id and d.location_id=l.location_id and city='South San Francisco'
 
--查找和143在同一个部门和同一个岗位的比他工资高的员工
select last_name||' '||first_name
from employees
where salary>(select salary from employees where employee_id=143)
and department_id=(select department_id from employees where employee_id=143)
and job_id=(select job_id from employees where employee_id=143)
 
--查询总工资最多的部门，显示部门编号，部门名
select *
from (select d.department_id,d.department_name,sum(e.salary) as salary
from employees e,departments d
where e.department_id=d.department_id
group by d.department_id,d.department_name
order by salary desc)
where rownum=1
15 创建表
创建一个LOL里的英雄表，用于存放各种英雄
hero即表名
number表示数字类型
varchar2(30)表示可变长字符类型，最长30

create table hero(
  id number,
  name varchar2(30),
  hp number,
  mp number,
  damage number,
  armor number,
  speed number
)

varchar和varchar2的区别
两种都是变长的，对于汉字(unicode)的处理有区别
varchar如果放的是英文和数字，就用一个字节存放，如果是汉字(unicode)，就用两个字节
varchar2 统一使用两个字节
一般都直接使用varchar2，使用varchar很少了
16 插入数据
insert into hero (id,name,hp,mp,damage,armor,speed) values(1,'炸弹人',450,200,45,3,300);
commit;

commit;的意思是提交这一次插入行为，否则数据库不会生效

17 序列
创建sequence   使用序列sequence作为id自增长
create sequence hero_seq
  increment by 1 
  start with 1 
  maxvalue 9999999

创建了一个名字为hero_seq的序列 
这个序列从1开始，每次增加1，最大值是9999999

获取hero_seq的下一个值
select hero_seq.nextval from dual

获取hero_seq的当前值
select hero_seq.currval from dual

sequence 作为id插入到表中
insert into hero (id,name,hp,mp,damage,armor,speed) values(hero_seq.nextval,'炸弹人',450,200,45,3,300);

select * from hero    id自增加的
18 修改数据
修改一个字段
update hero set damage = 46 where id = 22;

修改多个字段
update hero set damage = 48, armor=4 where id = 22;
通常修改数据的时候，后面都会通过id指定某一条数据进行修改
如果不跟where条件，即意味着修改所有的行
19 删除数据
删除数据
delete from hero where id = 21
delete操作时可以回滚的，只要在提交之前，进行回滚就可以把删除的数据还原

删除一个表里的所有数据
delete from hero
不跟where条件，即删除一个表所有数据
20 舍去表
舍去表
truncate table hero
truncate 指令 立即删除表里所有的记录 
并且 不能回滚！
truncate 比delete更快，更彻底，并且不能回复，不能回滚
所以只有在确定要删除一个表所有记录的时候，才能执行这个操作
21 修改表结构
增加新的一列
alter table hero add (kills number)

修改列
alter table hero modify(name varchar2(300))

删除列
alter table hero drop column kills
22 删除表
drop table hero
不能回滚，所以删除表一定要非常谨慎
23 阶段练习4
为hr方案创建表“USERS”，要求有列：USER_ID,USER_NAME,BIRTHDATE,AGE,SALARY,photo,USER_COMMENT(个人履历)

create table hr.users (
user_id number,
user_name varchar2(30),
BIRTHDATE date,
AGE number,
SALARY number,
photo varchar2(30),
USER_COMMENT varchar2(3000)
)
select * from hr.users

为“USERS” 表添加一列 “DEPARTMENT_ID”;
alter table hr.users add (department_id number)

删除表“USERS”中的age列
alter table hr.users drop column age

删除表“users”
drop table hr.users
24 约束
约束种类 
唯一约束 unique 不可重复 
非空约束 not null 不能为空必须制定值 
主键约束 primary key 通常是给id使用的，同时具备了unique和not null约束 
外键约束 foreign key

创建表的时候设置约束 以唯一约束为例子：首先删除表hero
drop table hero
然后再重新创建表hero,并且在创建表hero的时候，设置约束
create table hero(
   id number,
   name varchar2(30) unique,
   hp number,
   mp number,
   damage number,
   armor number,
   speed number
)
通过关键字unique给name加上了唯一约束
增加数据的时候，name 就不能重复了
这个时候重复执行下面的sql语句，加入相同的name的数据就会提示出错
insert into hero (id,name,hp,mp,damage,armor,speed) values(1,'炸弹',450,200,45,4,30);

给约束命名
上一步提示违反的约束名称是 SYS.SYS_C003997 这个名字是系统自动分配的。 可以在创建表的时候指定约束名称
create table hero(
   id number,
   name varchar2(30),
   hp number,
   mp number,
   damage number,
   armor number,
   speed number,
   constraint uk_hero_name unique (name)
)
uk_hero_name即指定的约束名称。
uk 代表 unique
hero 代表表名
name 代表字段
约束名称是自定义的，但是易读性高的约束命名方式，更加便于调试与维护
当加入name存在的数据的时候，就会提示违反uk_hero_name这个约束

insert into hero (id,name,hp,mp,damage,armor,speed) values(1,'炸弹',450,200,45,4,30);
给已经存在的表增加约束
alter table hero add constraint pk_hero_id  primary key (id)
给已经存在的表hero的id字段加上主键约束

删除约束
alter table hero drop constraint uk_hero_name

创建外键约束
外键指的是本表的某一个字段，指向另一张表的主键（或者是有唯一约束的字段）
击杀记录表，用于记录英雄彼此之间的击杀数量
create table kill_record(
   id number,
   killerid number,
   killedid number,
   number_ number,
   constraint fk_killer_hero foreign key (killerid) references hero(id),
   constraint fk_killed_hero foreign key (killedid) references hero(id)
)
killerid 存放击杀英雄的id
killedid 存放被击杀英雄的id
number_ 存放击杀了多少次（因为number是关键字，所以使用number_作为字段名）
假设hero中分别有id=2,3的记录，那么
insert into kill_record (id,killerid,killedid,number_) values(1,2,3,1);
即表示英雄2 击杀了 英雄3 一次

约束的作用，是保证插入的数据的一致性，所以在试图插入数据
insert into kill_record (id,killerid,killedid,number_) values(1,2,300,1);
的时候会失败，因为hero表里没有id=300的数据，这样就违反了外键约束
25 视图
创建一个视图
create view herosimple as (
    select id, name from hero
)
select * from herosimple;
这样就可以对herosimple像操作普通表一样进行操作了

修改table会导致view里看到的数据发生变化
update hero set name='炸弹人1' where id = 1
select * from herosimple

修改视图，会不会导致表的数据发生变化？   会导致表的数据发生变化
update herosimple set name='炸弹人2' where id = 2
select * from hero
26 阶段练习5
为hr.users表的user_id添加主键约束。
alter table hr.users add constraint pk_users_id primary key (user_id)

为hr.users表的user_name添加非空和唯一约束。
alter table hr.users modify(user_name varchar2(300) not null)
alter table hr.users add constraint uk_users_name unique (user_name);

创建一个表hr.department( department_id, department_name)
create table hr.department (
department_id number primary key,
department_name varchar(30)
)

为hr.users表的DEPARTMENT_ID添加外键约束
alter table hr.users add (department_id number)
alter table hr.users add constraint fk_users_department foreign key (department_id) references hr.department(department_id)

删除表hr.users后重新创建它，并在一条SQL语句中一并添加上诉的约束条件。
drop table hr.users;
 
create table hr.users (
  user_id number primary key,
  user_name varchar2(30) not null unique,
  BIRTHDATE date,
  AGE number,
  SALARY number,
  photo varchar2(30),
  USER_COMMENT varchar2(3000),
  department_id ,
  constraint fk_users_department foreign key (department_id) references hr.department(department_id)
)
27 创建用户
数据库 new
用户名比如bigsword
名字: bigsword 
密码: bigsword 
Default tablespace: USERS 
Temporary tablespace: TEMP
对象权限
 
角色权限
 
系统权限
 
apply确认
 
登陆的时候，使用Normal身份
以后再使用bigsword等的时候，一定要使用Normal 身份登陆，这样使用bigsword身份创建的表，才可以通过jdbc访问呢
 

