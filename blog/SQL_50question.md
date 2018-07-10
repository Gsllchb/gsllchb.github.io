# 50道SQL经典题
题目转载自[50道SQL练习题](https://zhuanlan.zhihu.com/p/32137597)。答案大都是我自己写的，部分答案借鉴了上文中提供的答案。另外，我在编写本
文时将题目大致按照由易到难的顺序做了重新编排。

由于我在数据库方面尚处于入门阶段，因此若是您发现本文中有任何错误或者可以改进的地方都请[联系并告知我](https://github.com/Gsllchb/gsllchb.github.io/issues)。


## 创建测试数据
创建student表：
```SQL
CREATE TABLE student(
    id UNSIGNED BIGINT NOT NULL PRIMARY KEY,
    name NVARCHAR(4) NOT NULL, 
    birthday DATETIME NOT NULL, 
    sex NCHAR(2) NOT NULL
);
INSERT INTO student VALUES(1, '赵雷' , '1990-01-01' , '男');
INSERT INTO student VALUES(2, '钱电' , '1990-12-21' , '男');
INSERT INTO student VALUES(3, '孙风' , '1990-05-20' , '男');
INSERT INTO student VALUES(4, '李云' , '1990-08-06' , '男');
INSERT INTO student VALUES(5, '周梅' , '1991-12-01' , '女');
INSERT INTO student VALUES(6, '吴兰' , '1992-03-01' , '女');
INSERT INTO student VALUES(7, '郑竹' , '1989-07-01' , '女');
INSERT INTO student VALUES(8, '张三' , '2017-12-20' , '女');
INSERT INTO student VALUES(9, '李四' , '2017-12-25' , '女');
INSERT INTO student VALUES(10, '李四' , '2017-12-30' , '女');
INSERT INTO student VALUES(11, '赵六' , '2017-01-01' , '女');
INSERT INTO student VALUES(12, '孙七' , '2018-01-01' , '女');
```
创建course表：
```SQL
CREATE TABLE course(
    id UNSIGNED BIGINT NOT NULL PRIMARY KEY,
    name NVARCHAR(4) NOT NULL,
    teacher_id UNSIGNED BIGINT
);
INSERT INTO course VALUES(1, '语文' , 2);
INSERT INTO course VALUES(2, '数学' , 1);
INSERT INTO course VALUES(3, '英语' , 3);
```
创建teacher表：
```SQL
CREATE TABLE teacher(
    id UNSIGNED BIGINT NOT NULL PRIMARY KEY,
    name NVARCHAR(4)
);
INSERT INTO teacher VALUES(1, '张三');
INSERT INTO teacher VALUES(2, '李四');
INSERT INTO teacher VALUES(3, '王五');
```
创建grade表（成绩表）：
```SQL
CREATE TABLE grade(
    id UNSIGNED BIGINT NOT NULL PRIMARY KEY,
    student_id UNSIGNED BIGINT NOT NULL,
    course_id UNSIGNED BIGINT NOT NULL,
    score DECIMAL(5,2)
);
INSERT INTO grade VALUES(1, 1, 1, 80);
INSERT INTO grade VALUES(2, 1, 2, 90);
INSERT INTO grade VALUES(3, 1, 3, 99);
INSERT INTO grade VALUES(4, 2, 1, 70);
INSERT INTO grade VALUES(5, 2, 2, 60);
INSERT INTO grade VALUES(6, 2, 3, 80);
INSERT INTO grade VALUES(7, 3, 1, 80);
INSERT INTO grade VALUES(8, 3, 2, 80);
INSERT INTO grade VALUES(9, 3, 3, 80);
INSERT INTO grade VALUES(10, 4, 1, 50);
INSERT INTO grade VALUES(11, 4, 2, 30);
INSERT INTO grade VALUES(12, 4, 3, 20);
INSERT INTO grade VALUES(13, 5, 1, 76);
INSERT INTO grade VALUES(14, 5, 2, 87);
INSERT INTO grade VALUES(15, 6, 1, 31);
INSERT INTO grade VALUES(16, 6, 3, 34);
INSERT INTO grade VALUES(17, 7, 2, 89);
INSERT INTO grade VALUES(18, 7, 3, 98);
```


## 题目及答案
1. 查询「李」姓老师的数量
```SQL
SELECT COUNT(*) FROM teacher
WHERE name LIKE "李%";
```

2. 查询 1990 年出生的学生名单
```SQL
SELECT * FROM student
WHERE YEAR(birthday) = 1990;
```

3. 查询名字中含有「风」字的学生信息
```SQL
SELECT * FROM student
WHERE name LIKE "%风%";
```

4. 求每门课程的学生人数
```SQL
SELECT grade.course_id, COUNT(student_id) FROM grade
GROUP BY course_id;
```

5. 查询本月过生日的学生
```SQL
SELECT * FROM student
WHERE MONTH(NOW()) = MONTH(birthday);
```

6. 查询下月过生日的学生
```SQL
SELECT * FROM student
WHERE MONTH(NOW()) + 1 = MONTH(birthday);
```

7. 查询本周过生日的学生
```SQL
SELECT * FROM student
WHERE WEEKOFYEAR(NOW()) = WEEKOFYEAR(birthday);
```

8. 查询下周过生日的学生
```SQL
SELECT * FROM student
WHERE WEEKOFYEAR(NOW()) + 1 = WEEKOFYEAR(birthday);
```

9. 查询各学生的年龄，只按年份来算
```SQL
SELECT *, YEAR(NOW()) - YEAR(birthday) AS 年龄 FROM student;
```

10. 按照出生日期来算，当前月日小于出生年月的月日则，年龄减一
```SQL
SELECT *, TIMESTAMPDIFF(YEAR, birthday, NOW()) AS 年龄 FROM student;
```

11. 检索至少选修两门课程的学生学号
```SQL
SELECT student_id FROM grade
GROUP BY student_id
HAVING COUNT(course_id) >= 2;
```

12. 统计每门课程的学生选修人数（超过5人的课程才统计）
```SQL
SELECT course_id, COUNT(*) FROM grade
GROUP BY course_id
HAVING COUNT(*) > 5;
```

13. 查询存在不及格成绩的课程
```SQL
SELECT course_id FROM grade
GROUP BY course_id
HAVING COUNT(score < 60) > 0;
```

14. 查询男生、女生人数
```SQL
SELECT sex, COUNT(*) FROM student
GROUP BY sex;
```

15. 查询每门课程被选修的学生数 
```SQL
SELECT course_id, COUNT(student_id) FROM grade
GROUP BY course_id;
```

16. 查询在grade表存在成绩的学生信息
```SQL
SELECT * FROM student
WHERE id IN (
    SELECT student_id FROM grade
);
```

17. 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列
```SQL
SELECT course_id, AVG(score) FROM grade
GROUP BY course_id
ORDER BY AVG(score) DESC, course_id;
```

18. 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 
```SQL
SELECT grade.student_id, grade.course_id as course_id1, t.course_id as course_id2, grade.score FROM grade, grade AS t
WHERE grade.student_id = t.student_id AND grade.course_id != t.course_id AND grade.score = t.score;
```

19. 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）
```SQL
SELECT student.*, grade.course_id, grade.score FROM student
LEFT JOIN grade ON student.id = grade.student_id;
```

20. 查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名
```SQL
SELECT student.id, student.name FROM student, grade
WHERE student.id = grade.student_id AND grade.course_id = 1 AND grade.score >= 80;
```

21. 查询每门功成绩最好的前两名
```SQL
SELECT * FROM grade
WHERE (
    SELECT COUNT(*) FROM grade AS t
    WHERE t.course_id = grade.course_id AND t.score > grade.score
) < 2
ORDER BY course_id, score DESC;
```

22. 查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数
```SQL
SELECT student.name as student_name, course.name as course_name, grade.score FROM student, course, grade
WHERE grade.score >= 70 AND student.id = grade.student_id AND grade.course_id = course.id;
```

23. 查询出只选修两门课程的学生学号和姓名
```SQL
SELECT t.student_id, student.name FROM (
    SELECT student_id FROM grade
    GROUP BY student_id
    HAVING COUNT(course_id) = 2
) AS t 
LEFT JOIN student ON t.student_id = student.id;
```

24. 查询同名同姓学生名单，并统计同名人数
```SQL
SELECT student.*, t.num FROM (
    SELECT name, COUNT(*) AS num FROM student
    GROUP BY name
    HAVING COUNT(*) > 1
) AS t, student
WHERE student.name = t.name;
```

25. 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数 
```SQL
SELECT student.name, grade.score FROM grade, student, course
WHERE course.name = "数学" AND course.id = grade.course_id AND grade.student_id = student.id AND grade.score < 60;
```

26. 查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩 
```SQL
SELECT t.*, student.name FROM (
    SELECT student_id, AVG(score) FROM grade
    GROUP BY student_id
    HAVING AVG(score) >= 85
) AS t
LEFT JOIN student ON t.student_id = student.id;
```

27. 检索1课程分数小于60，按分数降序排列的学生信息
```SQL
SELECT student.* FROM student, grade
WHERE student.id = grade.student_id and grade.course_id = 1 and score < 60
ORDER BY score DESC;
```

28. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩
```SQL
SELECT grade.*, t.avg_score FROM 
grade LEFT JOIN (
    SELECT student_id, AVG(score) AS avg_score FROM grade
    GROUP BY student_id
) AS t ON t.student_id = grade.student_id
ORDER BY t.avg_score DESC;
```

29. 查询选修了全部课程的学生信息
```SQL
SELECT student.* FROM (
    SELECT student_id FROM grade
    GROUP BY student_id
    HAVING COUNT(*) = (
        SELECT COUNT(*) FROM course
    )
) AS t
LEFT JOIN student ON t.student_id = student.id;
```

30. 查询至少有一门课与学号为1的同学所学相同的同学的信息
```SQL
SELECT DISTINCT student.* FROM student, grade
WHERE student.id = grade.student_id AND grade.course_id IN (
    SELECT course_id FROM grade
    WHERE student_id = 1
);
```

31. 查询没有学全所有课程的同学的信息
```SQL
SELECT * FROM student
WHERE id NOT IN (
    SELECT student_id FROM grade
    GROUP BY student_id
    HAVING COUNT(course_id) = (
        SELECT COUNT(course_id) FROM course
    )
);
```

32. 查询没学过"张三"老师讲授的任一门课程的学生姓名
```SQL
SELECT name FROM student
WHERE id NOT IN (
    SELECT grade.student_id FROM grade, course, teacher
    WHERE grade.course_id = course.id AND course.teacher_id = teacher.id AND teacher.name = "张三"
);
```

33. 查询各科成绩前三名的记录
```SQL
SELECT * FROM grade 
WHERE (
    SELECT COUNT(*) FROM grade AS t
    WHERE grade.course_id = t.course_id AND grade.score < t.score
) < 3 
ORDER BY course_id, score DESC;
```

34. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为NULL)
```SQL
SELECT t1.id, t1.name, t2.course_amount, t2.sum_score FROM student AS t1
LEFT JOIN (
    SELECT student_id, COUNT(course_id) AS course_amount, SUM(score) AS sum_score FROM grade
    GROUP BY student_id
) AS t2 ON t1.id = t2.student_id;
```

35. 查询学过「张三」老师授课的同学的信息
```SQL
SELECT DISTINCT student.* FROM student, grade, course, teacher
WHERE teacher.name = "张三" AND teacher.id = course.teacher_id AND course.id = grade.course_id AND grade.student_id = student.id;
```


36. 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩
```SQL
SELECT t1.student_id, t2.name, t1.avg_score FROM (
    SELECT student_id, AVG(score) AS avg_score FROM grade
    GROUP BY student_id
    HAVING AVG(score) >= 60
) AS t1
LEFT JOIN (
    SELECT id, name FROM student
) AS t2 ON t1.student_id = t2.id;
```

37. 查询1课程比2课程成绩高的学生的信息及课程分数
```SQL
SELECT t1.student_id, t1.score AS c1_score, t2.score AS c2_score FROM (
    SELECT student_id, score FROM grade
    WHERE course_id = 1
) AS t1, (
    SELECT student_id, score FROM grade
    WHERE course_id=2
) AS t2
WHERE t1.student_id = t2.student_id AND t1.score > t2.score;
```

38. 查询同时存在1课程和2课程成绩的学生的信息及课程分数
```SQL
SELECT t1.student_id, t1.score AS c1_score, t2.score AS c2_score FROM (
    SELECT student_id, score FROM grade
    WHERE course_id=1
) AS t1
INNER JOIN (
    SELECT student_id, score FROM grade
    WHERE course_id=2
) AS t2 ON t1.student_id = t2.student_id;
```

39. 查询存在" 01 "课程但可能不存在2课程成绩(不存在时显示为NULL）的学生的信息及课程分数
```SQL
SELECT t1.student_id, t1.score AS c1_score, t2.score AS c2_score FROM (
    SELECT student_id, score FROM grade
    WHERE course_id=1
) AS t1 
LEFT JOIN (
    SELECT student_id, score FROM grade
    WHERE course_id=2
) AS t2 ON t1.student_id = t2.student_id;
```

40. 查询不存在1课程但存在2课程成绩的学生的信息及课程分数
```SQL
SELECT student_id, score AS c2_score FROM grade
WHERE course_id = 2 AND student_id NOT IN (
    SELECT student_id FROM grade 
    WHERE course_id=1
);
```

41. 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩
```SQL
SELECT t.student_id, student.name, t.avg_score FROM (
    SELECT student_id, AVG(score) AS avg_score FROM grade 
    WHERE student_id IN (
        SELECT student_id FROM grade 
        WHERE score < 60 
        GROUP BY student_id 
        HAVING COUNT(score) >= 2
    )
    GROUP BY student_id
) AS t
LEFT JOIN student ON t.student_id = student.id;
```

42. 查询和1号的同学学习的课程完全相同的其他同学的信息
```SQL
SELECT * FROM student 
WHERE student.id NOT IN (
    SELECT t2.id FROM (
        SELECT student.id, t1.course_id FROM student, (
            SELECT grade.course_id FROM grade
            WHERE grade.student_id = 1
        ) AS t1
    ) AS t2
    LEFT JOIN grade ON t2.id = grade.student_id AND t2.course_id = grade.course_id
    WHERE grade.course_id IS NULL
) AND student.id != 1;
```

43. 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩
```SQL
SELECT grade.course_id, grade.score, student.* FROM teacher, grade, course, student
WHERE student.id = grade.student_id AND teacher.name = "张三" AND teacher.id = course.teacher_id AND course.id = grade.course_id AND (
    SELECT COUNT(*) FROM grade AS t 
    WHERE t.course_id = grade.course_id AND t.score > grade.score
) = 0;
```

44. 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩
```SQL
# 答案同43
```

45. 查询各科成绩最高分、最低分和平均分：
以如下形式显示：课程 ID，课程 name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率
（及格：>=60，中等：70-80，优良：80-90，优秀：>=90）
要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列
```SQL
SELECT t.*, course.name FROM (
    SELECT course_id, MAX(score) AS 最高分, MIN(score) AS 最低分, AVG(score) AS 平均分, COUNT(*) AS 选课人数, (SUM(
        CASE 
            WHEN score >= 60 
            THEN 1 
            ELSE 0 
        END
    ) / COUNT(*)) AS 及格率, (SUM(
        CASE 
            WHEN score BETWEEN 70 AND 79 
            THEN 1 
            ELSE 0 
        END
    ) / COUNT(*)) AS 中等率, (SUM(
        CASE 
            WHEN score BETWEEN 80 AND 89 
            THEN 1 
            ELSE 0
        END
    ) / COUNT(*)) AS 优良率, (SUM(
        CASE 
            WHEN score >= 90 
            THEN 1 
            ELSE 0 
        END
    ) / COUNT(*)) AS 优秀率 FROM grade
    GROUP BY course_id
    ORDER BY COUNT(score) DESC, course_id
) AS t 
LEFT JOIN course ON course.id = t.course_id;
```

46. 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比
```SQL
SELECT t1.*, course.name FROM (
    SELECT course_id, (SUM(
        CASE
            WHEN score BETWEEN 85 AND 100 
            THEN 1 
            ELSE 0 
        END
    ) / COUNT(*)) AS "100-85", (SUM(
        CASE 
            WHEN score BETWEEN 70 AND 84 
            THEN 1 
            ELSE 0 
        END
    ) / COUNT(*)) AS "85-70",(SUM(
        CASE 
            WHEN score BETWEEN 60 AND 69 
            THEN 1 
            ELSE 0 
        END
    ) / COUNT(*)) AS "70-60",(SUM(
        CASE 
            WHEN score BETWEEN 0 AND 59 
            THEN 1 
            ELSE 0 
        END
    ) / COUNT(*)) AS "60-0" FROM grade
    GROUP BY course_id
) AS t1
RIGHT JOIN course ON t1.course_id = course.id;
```

47. 查询学生的总成绩，并进行排名，总分重复时保留名次空缺
```SQL
SELECT t1.*, @currank := @currank + 1 AS rank FROM (
    SELECT grade.student_id, SUM(score) FROM grade
    GROUP BY grade.student_id  
    ORDER BY SUM(score) DESC
) AS t1, (
    SELECT @currank := 0
) AS t;
```

48. 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺
```SQL
SELECT t1.*, (
    CASE 
        WHEN @fontscore = t1.sumscore 
        THEN @currank
        WHEN @fontscore := t1.sumscore 
        THEN @currank := @currank+1
    END
    ) AS rank FROM (
    SELECT grade.student_id, SUM(score) AS sumscore FROM grade
    GROUP BY grade.student_id 
    ORDER BY SUM(score) DESC
) AS t1, (
    SELECT @currank := 0, @fontscore := NULL
) AS t;
```

49. 按各科成绩进行排序，并显示排名，Score重复时保留名次空缺
```SQL
SELECT grade.student_id, grade.course_id, (
    CASE 
        WHEN @pre_parent_code = grade.course_id 
        THEN @curRank := @curRank + 1 
        WHEN @pre_parent_code := grade.course_id 
        THEN @curRank := 1
    END AS rank, grade.score FROM (
    SELECT @curRank := 0, @pre_parent_code := ''
) AS t, grade
ORDER BY grade.course_id, grade.score DESC;
```

50. 按各科成绩进行排序，并显示排名，Score重复时合并名次
```SQL
SELECT grade.student_id, grade.course_id, (
    CASE 
        WHEN @pre_parent_code = grade.course_id 
        THEN (
            CASE
                WHEN @prefontscore = grade.score
                THEN @curRank 
                WHEN @prefontscore := grade.score 
                THEN @curRank := @curRank + 1 
            END
        ) 
        WHEN @prefontscore := grade.score 
        THEN @curRank := 1
    END
) AS rank, grade.score, @pre_parent_code := grade.course_id FROM (
    SELECT @curRank := 0, @pre_parent_code := '', @prefontscore := NULL
) AS t, grade
ORDER BY grade.course_id, grade.score DESC;
```