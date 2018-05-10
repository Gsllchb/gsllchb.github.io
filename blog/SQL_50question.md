# 50道SQL经典题
题目转载自[50道SQL练习题](https://zhuanlan.zhihu.com/p/32137597)。答案是我自己写的，但是部分答案借鉴了上文中提供的答案。以下所有SQL语句均
在MySQL5.7上测试运行过。另外，我在编写本文时将题目大致按照由易到难的顺序做了重新编排。

由于我在数据库方面尚处于入门阶段，因此若是您发现本文中有任何错误或者可以改进的地方都请[联系并告知我](https://github.com/Gsllchb/gsllchb.github.io/issues)。


## 创建测试数据
创建Student表：
```SQL
CREATE TABLE Student(SId VARCHAR(10),Sname VARCHAR(10),Sage DATETIME,Ssex VARCHAR(10));
INSERT INTO Student VALUES('01' , '赵雷' , '1990-01-01' , '男');
INSERT INTO Student VALUES('02' , '钱电' , '1990-12-21' , '男');
INSERT INTO Student VALUES('03' , '孙风' , '1990-05-20' , '男');
INSERT INTO Student VALUES('04' , '李云' , '1990-08-06' , '男');
INSERT INTO Student VALUES('05' , '周梅' , '1991-12-01' , '女');
INSERT INTO Student VALUES('06' , '吴兰' , '1992-03-01' , '女');
INSERT INTO Student VALUES('07' , '郑竹' , '1989-07-01' , '女');
INSERT INTO Student VALUES('09' , '张三' , '2017-12-20' , '女');
INSERT INTO Student VALUES('10' , '李四' , '2017-12-25' , '女');
INSERT INTO Student VALUES('11' , '李四' , '2017-12-30' , '女');
INSERT INTO Student VALUES('12' , '赵六' , '2017-01-01' , '女');
INSERT INTO Student VALUES('13' , '孙七' , '2018-01-01' , '女');
```
创建Course表：
```SQL
CREATE TABLE Course(CId VARCHAR(10),Cname NVARCHAR(10),TId VARCHAR(10));
INSERT INTO Course VALUES('01' , '语文' , '02');
INSERT INTO Course VALUES('02' , '数学' , '01');
INSERT INTO Course VALUES('03' , '英语' , '03');
```
创建Teacher表：
```SQL
CREATE TABLE Teacher(TId VARCHAR(10),Tname VARCHAR(10));
INSERT INTO Teacher VALUES('01' , '张三');
INSERT INTO Teacher VALUES('02' , '李四');
INSERT INTO Teacher VALUES('03' , '王五');
```
创建SC表（成绩表）：
```SQL
CREATE TABLE SC(SId VARCHAR(10),CId VARCHAR(10),score DECIMAL(18,1));
INSERT INTO SC VALUES('01' , '01' , 80);
INSERT INTO SC VALUES('01' , '02' , 90);
INSERT INTO SC VALUES('01' , '03' , 99);
INSERT INTO SC VALUES('02' , '01' , 70);
INSERT INTO SC VALUES('02' , '02' , 60);
INSERT INTO SC VALUES('02' , '03' , 80);
INSERT INTO SC VALUES('03' , '01' , 80);
INSERT INTO SC VALUES('03' , '02' , 80);
INSERT INTO SC VALUES('03' , '03' , 80);
INSERT INTO SC VALUES('04' , '01' , 50);
INSERT INTO SC VALUES('04' , '02' , 30);
INSERT INTO SC VALUES('04' , '03' , 20);
INSERT INTO SC VALUES('05' , '01' , 76);
INSERT INTO SC VALUES('05' , '02' , 87);
INSERT INTO SC VALUES('06' , '01' , 31);
INSERT INTO SC VALUES('06' , '03' , 34);
INSERT INTO SC VALUES('07' , '02' , 89);
INSERT INTO SC VALUES('07' , '03' , 98);
```


## 题目及答案
1. 查询「李」姓老师的数量
```SQL
SELECT COUNT(*) FROM Teacher
WHERE Tname LIKE "李%";
```

2. 查询 1990 年出生的学生名单
```SQL
SELECT * FROM Student
WHERE YEAR(Sage) = 1990;
```

3. 查询名字中含有「风」字的学生信息
```SQL
SELECT * FROM Student
WHERE Sname LIKE "%风%";
```

4. 求每门课程的学生人数
```SQL
SELECT SC.CId, COUNT(SId) FROM SC
GROUP BY CId;
```

5. 查询本月过生日的学生
```SQL
SELECT * FROM Student
WHERE MONTH(NOW()) = MONTH(Sage);
```

6. 查询下月过生日的学生
```SQL
SELECT * FROM Student
WHERE MONTH(NOW()) + 1 = MONTH(Sage);
```

7. 查询本周过生日的学生
```SQL
SELECT * FROM Student
WHERE WEEKOFYEAR(NOW()) = WEEKOFYEAR(Sage);
```

8. 查询下周过生日的学生
```SQL
SELECT * FROM Student
WHERE WEEKOFYEAR(NOW()) + 1 = WEEKOFYEAR(Sage);
```

9. 查询各学生的年龄，只按年份来算
```SQL
SELECT *, YEAR(NOW()) - YEAR(Sage) AS 年龄 FROM Student;
```

10. 按照出生日期来算，当前月日小于出生年月的月日则，年龄减一
```SQL
SELECT *, TIMESTAMPDIFF(YEAR, Sage, NOW()) AS 年龄 FROM Student;
```

11. 检索至少选修两门课程的学生学号
```SQL
SELECT SId FROM SC
GROUP BY SId
HAVING COUNT(CId) >= 2;
```

12. 统计每门课程的学生选修人数（超过5人的课程才统计）
```SQL
SELECT CId, COUNT(*) FROM SC
GROUP BY CId
HAVING COUNT(*) > 5;
```

13. 查询存在不及格成绩的课程
```SQL
SELECT CId FROM SC
GROUP BY CId
HAVING COUNT(score < 60) > 0;
```

14. 查询男生、女生人数
```SQL
SELECT Ssex, COUNT(*) FROM Student
GROUP BY Ssex;
```

15. 查询每门课程被选修的学生数 
```SQL
SELECT CId, COUNT(SId) FROM SC
GROUP BY CId;
```

16. 查询在SC表存在成绩的学生信息
```SQL
SELECT * FROM Student
WHERE SId IN (
    SELECT SId FROM SC
);
```

17. 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列
```SQL
SELECT CId, AVG(score) FROM SC
GROUP BY CId
ORDER BY AVG(score) DESC, CId;
```

18. 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 
```SQL
SELECT DISTINCT SC.* FROM SC, SC AS t
WHERE SC.SId = t.SId AND SC.CId != t.CId AND SC.score = t.score;
```

19. 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）
```SQL
SELECT Student.*, SC.CId, SC.score FROM Student
LEFT JOIN SC ON Student.SId = SC.SId;
```

20. 查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名
```SQL
SELECT Student.SId, Student.Sname FROM Student, SC
WHERE Student.SId = SC.SId AND SC.CId = "01" AND SC.score >= 80;
```

21. 查询每门功成绩最好的前两名
```SQL
SELECT * FROM SC
WHERE (
    SELECT COUNT(*) FROM SC AS t
    WHERE t.CId = SC.CId AND t.score > SC.score
) < 2
ORDER BY CId, score DESC;
```

22. 查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数
```SQL
SELECT Student.Sname, Course.Cname, SC.score FROM Student, Course, SC
WHERE SC.score >= 70 AND Student.SId = SC.SId AND SC.CId = Course.CId;
```

23. 查询出只选修两门课程的学生学号和姓名
```SQL
SELECT t.SId, Student.Sname FROM (
    SELECT SId FROM SC
    GROUP BY SId
    HAVING COUNT(CId) = 2
) AS t 
LEFT JOIN Student ON t.SId = Student.SId;
```

24. 查询同名同姓学生名单，并统计同名人数
```SQL
SELECT Student.*, t.num FROM (
    SELECT Sname, COUNT(*) AS num FROM Student
    GROUP BY Sname
    HAVING COUNT(*) > 1
) AS t, Student
WHERE Student.Sname = t.Sname;
```

25. 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数 
```SQL
SELECT Student.Sname, SC.score FROM SC, Student, Course
WHERE Course.Cname = "数学" AND Course.CId = SC.CId AND SC.SId = Student.SId AND SC.score < 60;
```

26. 查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩 
```SQL
SELECT t.*, Student.Sname FROM (
    SELECT SId, AVG(score) FROM SC
    GROUP BY SId
    HAVING AVG(score) >= 85
) AS t
LEFT JOIN Student ON t.SId = Student.SId;
```

27. 检索"01"课程分数小于60，按分数降序排列的学生信息
```SQL
SELECT Student.* FROM Student, SC
WHERE Student.SId = SC.SId and SC.CId = "01" and score < 60
ORDER BY score DESC;
```

28. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩
```SQL
SELECT SC.*, t.avg_score FROM (
    SELECT SId, AVG(score) AS avg_score FROM SC
    GROUP BY SId
) AS t
RIGHT JOIN SC ON t.SId = SC.SId
ORDER BY t.avg_score DESC;
```

29. 查询选修了全部课程的学生信息
```SQL
SELECT Student.* FROM (
    SELECT SId FROM SC
    GROUP BY SId
    HAVING COUNT(*) = (
        SELECT COUNT(*) FROM Course
    )
) AS t
LEFT JOIN Student ON t.SId = Student.SId;
```

30. 查询至少有一门课与学号为"01"的同学所学相同的同学的信息
```SQL
SELECT DISTINCT Student.* FROM Student, SC
WHERE Student.SId = SC.SId AND SC.CId IN (
    SELECT CId FROM SC
    WHERE SId = "01"
);
```

31. 查询没有学全所有课程的同学的信息
```SQL
SELECT * FROM Student
WHERE SId NOT IN (
    SELECT SId FROM SC
    GROUP BY SId
    HAVING COUNT(CId) = (
        SELECT COUNT(CId) FROM Course
    )
);
```

32. 查询没学过"张三"老师讲授的任一门课程的学生姓名
```SQL
SELECT Sname FROM Student
WHERE SId NOT IN (
    SELECT SC.SId FROM SC, Course, Teacher
    WHERE SC.CId = Course.CId AND Course.TId = Teacher.TId AND Teacher.Tname = "张三"
);
```

33. 查询各科成绩前三名的记录
```SQL
SELECT * FROM SC 
WHERE (
    SELECT COUNT(*) FROM SC AS t
    WHERE SC.CId = t.CId AND SC.score < t.score
) < 3 
ORDER BY CId, score DESC;
```

34. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为NULL)
```SQL
SELECT t1.SId, t1.Sname, t2.Camount, t2.sum_score FROM Student AS t1
LEFT JOIN (
    SELECT SId, COUNT(CId) AS Camount, SUM(score) AS sum_score FROM SC
    GROUP BY SId
) AS t2 ON t1.Sid = t2.SId;
```

35. 查询学过「张三」老师授课的同学的信息
```SQL
SELECT Student.* FROM Student, SC, Course, Teacher
WHERE Teacher.Tname = "张三" AND Teacher.TId = Course.TId AND Course.CId = SC.CId AND SC.SId = Student.SId;
```


36. 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩
```SQL
SELECT t1.SId, t2.Sname, t1.avg_score FROM (
    SELECT SId, AVG(score) AS avg_score FROM SC
    GROUP BY SId
    HAVING AVG(score) >= 60
) AS t1
LEFT JOIN (
    SELECT SId, Sname FROM Student
) AS t2 ON t1.SId = t2.SId;
```

37. 查询"01"课程比"02"课程成绩高的学生的信息及课程分数
```SQL
SELECT t1.SId, t1.score AS C01_score, t2.score AS C02_score FROM (
    SELECT SId, score FROM SC
    WHERE CId = "01"
) AS t1, (
    SELECT SId, score FROM SC
    WHERE CId="02"
) AS t2
WHERE t1.SId = t2.SId AND t1.score > t2.score;
```

38. 查询同时存在"01"课程和"02"课程成绩的学生的信息及课程分数
```SQL
SELECT t1.SId, t1.score AS C01_score, t2.score AS C02_score FROM (
    SELECT SId, score FROM SC
    WHERE CId="01"
) AS t1
INNER JOIN (
    SELECT SId, score FROM SC
    WHERE CId="02"
) AS t2 ON t1.SId = t2.SId;
```

39. 查询存在" 01 "课程但可能不存在"02"课程成绩(不存在时显示为NULL）的学生的信息及课程分数
```SQL
SELECT t1.SId, t1.score AS C01_score, t2.score AS C02_score FROM (
    SELECT SId, score FROM SC
    WHERE CId="01"
) AS t1 
LEFT JOIN (
    SELECT SId, score FROM SC
    WHERE CId="02"
) AS t2 ON t1.SId = t2.SId;
```

40. 查询不存在" 01 "课程但存在" 02 "课程成绩的学生的信息及课程分数
```SQL
SELECT SId, score AS C02_score FROM SC
WHERE CId = "02" AND SId NOT IN (
    SELECT SId FROM SC 
    WHERE CId="01"
);
```

41. 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩
```SQL
SELECT t.SId, Student.Sname, t.avg_score FROM (
    SELECT SId, AVG(score) AS avg_score FROM SC 
    WHERE SId IN (
        SELECT SId FROM SC 
        WHERE score < 60 
        GROUP BY SId 
        HAVING COUNT(score) >= 2
    )
    GROUP BY SId
) AS t
LEFT JOIN Student ON t.SId = Student.SId;
```

42. 查询和" 01 "号的同学学习的课程 完全相同的其他同学的信息
```SQL
SELECT * FROM Student 
WHERE Student.SId NOT IN (
    SELECT t1.SId FROM (
        SELECT Student.SId, t.CId FROM Student, (
            SELECT SC.CId FROM SC
            WHERE SC.SId='01'
        ) AS t
    ) AS t1
    LEFT JOIN SC ON t1.SId = SC.SId AND t1.CId = SC.CId
    WHERE SC.CId IS NULL
) AND Student.SId != '01';
```

43. 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩
```SQL
SELECT SC.CId, SC.score, Student.* FROM Teacher, SC, Course, Student 
WHERE Student.SId = SC.SId AND Teacher.Tname = "张三" AND Teacher.TId = Course.TId AND Course.CId = SC.CId AND (
    SELECT COUNT(*) FROM SC AS t 
    WHERE t.CId = SC.CId AND t.score > SC.score
) = 0;
```

44. 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩
同43

45. 查询各科成绩最高分、最低分和平均分：
以如下形式显示：课程 ID，课程 name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率
（及格：>=60，中等：70-80，优良：80-90，优秀：>=90）
要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列
```SQL
SELECT t.*, Course.Cname FROM (
    SELECT CId, MAX(score) AS 最高分, MIN(score) AS 最低分, AVG(score) AS 平均分, COUNT(*) AS 选课人数, (SUM(
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
    ) / COUNT(*)) AS 优秀率 FROM SC
    GROUP BY CId
    ORDER BY COUNT(score) DESC, CId
) AS t 
LEFT JOIN Course ON Course.CId = t.CId;
```

46. 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比
```SQL
SELECT t1.*, Course.Cname FROM (
    SELECT CId, (SUM(
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
    ) / COUNT(*)) AS "60-0" FROM SC
    GROUP BY CId
) AS t1
RIGHT JOIN Course ON t1.CId = Course.CId;
```

47. 查询学生的总成绩，并进行排名，总分重复时保留名次空缺
```SQL
SELECT t1.*, @currank := @currank + 1 AS rank FROM (
    SELECT SC.SId, SUM(score) FROM SC
    GROUP BY SC.SId  
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
    SELECT SC.SId, SUM(score) AS sumscore FROM SC
    GROUP BY SC.SId 
    ORDER BY SUM(score) DESC
) AS t1, (
    SELECT @currank := 0, @fontscore := NULL
) AS t;
```

49. 按各科成绩进行排序，并显示排名，Score重复时保留名次空缺
```SQL
SELECT SC.SId, SC.CId, (
    CASE 
        WHEN @pre_parent_code = SC.CId 
        THEN @curRank := @curRank + 1 
        WHEN @pre_parent_code := SC.CId 
        THEN @curRank := 1
    END AS rank, SC.score FROM (
    SELECT @curRank := 0, @pre_parent_code := ''
) AS t, SC
ORDER BY SC.CId, SC.score DESC;
```

50. 按各科成绩进行排序，并显示排名，Score重复时合并名次
```SQL
SELECT SC.SId, SC.CId, (
    CASE 
        WHEN @pre_parent_code = SC.CId 
        THEN (
            CASE
                WHEN @prefontscore = SC.score
                THEN @curRank 
                WHEN @prefontscore := SC.score 
                THEN @curRank := @curRank + 1 
            END
        ) 
        WHEN @prefontscore := SC.score 
        THEN @curRank := 1
    END
) AS rank, SC.score, @pre_parent_code := SC.CId FROM (
    SELECT @curRank := 0, @pre_parent_code := '', @prefontscore := NULL
) AS t, SC
ORDER BY SC.CId,SC.score DESC;
```