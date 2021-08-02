# C#开发中linq与lambda写法对照

C#开发中linq与lambda写法对照，在使用C#编程过程中，有些码农在实现查询功能的时候纠结于到底使用linq还是lambda来写代码，有些还搞不清楚，现就两者之间写汗的比较整理一个表格供参考。



## linq与lambda写法对照

| **SQL**                                                      | **LINQ**                                                     | **Lambda**                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| SELECT * FROM Employee                                       | from e in Employees select e                                 | Employees.Select ()                                          |
| SELECT e.LoginID,e.JobTitle FROM Employee AS e               | from e in Employees select new {e.LoginID, e.JobTitle}       | Employees.Select ( e => new{LoginID = e.LoginID,JobTitle = e.JobTitle} ) |
| SELECT e.LoginID AS ID, e.JobTitle AS Title FROM Employee AS e | from e in Employees select new {ID = e.LoginID, Title = e.JobTitle} | Employees.Select (e => new{ID = e.LoginID,Title = e.JobTitle } ) |
| SELECT DISTINCT e.JobTitle FROM Employee AS e                | (from e in Employees select e.JobTitle).Distinct()           | Employees.Select (e => e.JobTitle) .Distinct ()              |
| SELECT e.* FROM Employee AS e WHERE e.LoginID = ‘test’       | from e in Employees where e.LoginID == “test” select e       | Employees.Where (e => (e.LoginID == “test”))                 |
| SELECT e.* FROM Employee AS e WHERE e.LoginID = ‘test’ AND e.SalariedFlag = 1 | from e in Employees where e.LoginID == “test” && e.SalariedFlag select e | Employees.Where (e => ((e.LoginID == “test”) && e.SalariedFlag)) |
| SELECT e.* FROM Employee AS e WHERE e.VacationHours >= 2 AND e.VacationHours <= 10 | from e in Employees where e.VacationHours >= 2 && e.VacationHours <= 10 select e | Employees.Where (e => (((Int32)(e.VacationHours) >= 2) && ((Int32)(e.VacationHours) <= 10))) |
| SELECT e.* FROM Employee AS e ORDER BY e.NationalIDNumber    | from e in Employees orderby e.NationalIDNumber select e      | Employees.OrderBy (e => e.NationalIDNumber)                  |
| SELECT e.* FROM Employee AS e ORDER BY e.HireDate DESC, e.NationalIDNumber | from e in Employees orderby e.HireDate descending, e.NationalIDNumber select e | Employees.OrderByDescending (e => e.HireDate).ThenBy (e => e.NationalIDNumber) |
| SELECT e.* FROM Employee AS e WHERE e.JobTitle LIKE ‘Vice%’ OR SUBSTRING(e.JobTitle, 0, 3) = ‘Pro’ | from e in Employees where e.JobTitle.StartsWith(“Vice”) \|\| e.JobTitle.Substring(0, 3) == “Pro” select e | Employees.Where (e => (e.JobTitle.StartsWith (“Vice”) \|\| (e.JobTitle.Substring (0, 3) == “Pro”))) |
| SELECT SUM(e.VacationHours) FROM Employee AS e               |                                                              | Employees.Sum(e => e.VacationHours);                         |
| SELECT COUNT(*) FROM Employee AS e                           |                                                              | Employees.Count();                                           |
| SELECT SUM(e.VacationHours) AS TotalVacations,e.JobTitle FROM Employee AS e GROUP BY e.JobTitle | from e in Employees group e by e.JobTitle into g select new {JobTitle = g.Key, TotalVacations = g.Sum(e => e.VacationHours)} | Employees.GroupBy (e => e.JobTitle) .Select (g =>new{JobTitle = g.Key, TotalVacations = g.Sum (e => (Int32)(e.VacationHours)) }) |
| SELECT e.JobTitle, SUM(e.VacationHours) AS TotalVacations FROM Employee AS e GROUP BY e.JobTitle HAVING e.COUNT(*) > 2 | from e in Employees group e by e.JobTitle into g where g.Count() > 2 select new {JobTitle = g.Key, TotalVacations = g.Sum(e => e.VacationHours)} | Employees.GroupBy (e => e.JobTitle) .Where (g => (g.Count () > 2)).Select ( g =>new{JobTitle = g.Key, TotalVacations = g.Sum (e => (Int32)(e.VacationHours))} ) |
| SELECT * FROM Product AS p,ProductReview AS pr               | from p in Products from pr in ProductReviews select new {p, pr} | Products.SelectMany (p => ProductReviews,(p, pr) =>new{ p = p, pr = pr}) |
| SELECT * FROM Product AS p INNER JOIN ProductReview AS pr ON p.ProductID = pr.ProductID | from p in Products join pr in ProductReviews on p.ProductID equals pr.ProductID select new {p, pr} | Products.Join (ProductReviews, p => p.ProductID,pr => pr.ProductID, (p, pr) =>new{p = p,pr = pr}) |
| SELECT * FROM Product AS p INNER JOIN ProductCostHistory AS pch ON p.ProductID = pch.ProductID AND p.SellStartDate = pch.StartDate | from p in Products join pch in ProductCostHistories on new {p.ProductID, StartDate = p.SellStartDate} equals new {pch.ProductID, StartDate = pch.StartDate}select new {p, pch} | Products.Join (ProductCostHistories, p =>new{ProductID = p.ProductID, StartDate = p.SellStartDate}, pch =>new{ProductID = pch.ProductID, StartDate = pch.StartDate}, (p, pch) =>new{p = p,pch = pch}) |
| SELECT * FROM Product AS p LEFT OUTER JOIN ProductReview AS pr ON p.ProductID = pr.ProductID | from p in Products join pr in ProductReviews on p.ProductID equals pr.ProductID into prodrev select new {p, prodrev} | Products.GroupJoin (ProductReviews, p => p.ProductID,pr => pr.ProductID,(p, prodrev) => new{p = p,prodrev = prodrev }) |
| SELECT p.ProductID AS ID FROM Product AS p UNION SELECT pr.ProductReviewID FROM ProductReview AS pr | (from p in Products select new {ID = p.ProductID}).Union(from pr in ProductReviews select new {ID = pr.ProductReviewID}) | Products.Select (p => new{ID =p.ProductID}).Union (ProductReviews .Select (pr =>new{ ID = pr.ProductReviewID})) |
| SELECT TOP (10) * FROM Product AS p WHERE p.StandardCost < 100 | (from p in Products where p.StandardCost < 100 select p).Take(10) | Products.Where (p => (p.StandardCost < 100)).Take (10)       |
| SELECT * FROM [Product] AS p WHERE p.ProductID IN (SELECT pr.ProductID FROM [ProductReview] AS [pr] WHERE pr.[Rating] = 5) | from p in Products where (from pr in ProductReviews where pr.Rating == 5 select pr.ProductID).Contains(p.ProductID)select p | Products.Where (p =>ProductReviews.Where (pr => (pr.Rating == 5)) .Select (pr => pr.ProductID).Contains (p.ProductID) |

linq与lambda写法对照

linq与lambda写法对照可以让你在写linq或者lambda时，更加的方便。

原文出处：江湖人士【江小编】

原文链接：https://jhrs.com/2018/12525.html

本文观点不代表Dotnet9立场，转载请联系原作者。

