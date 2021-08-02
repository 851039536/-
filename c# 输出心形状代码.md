## c# 输出心形状代码

```c#
 static void Main(string[] args)
        {
           

            string outString = "*";//输出的字
            for (int i = 1; i <= 8; i++)
            {
                for (int j = 1; j <= 9; j++)
                {
                    if (((i == 1) && ((j == 5) || (j == 1) || (j == 9))) || ((i >= 4) && ((j <= i - 3) || (j >= 13 - i))))
                    {
                        Console.Write("  ");
                    }
                    else
                    {
                        Console.Write(" " + outString);//英文或是字符，符号输出方式
                         Console.Write(outString);//中文输出方式
                    }
                }
                Console.WriteLine("");
                Console.ReadLine();
            }


            for (int i = 1; i <= 8; i++)
            {
                for (int j = 1; j <= 9; j++)
                {
                    if (((i == 1) && ((j == 5) || (j == 1) || (j == 9))) || ((i >= 4) && ((j <= i - 3) || (j >= 13 - i))))
                    {
                        Console.Write("  ");
                    }
                    else
                    {
                        Console.Write(" " + outString);//英文或是字符，符号输出方式
                        // Console.Write(outString);//中文输出方式
                    }
                }
                Console.WriteLine("");
                Console.ReadLine();

            }

            float y, x, z, f;
            for (y = 1.5f; y > -1.5f; y -= 0.1f)
            {
                for (x = -1.5f; x < 1.5f; x += 0.05f)
                {
                    z = x * x + y * y - 1;
                    f = z * z * z - x * x * y * y * y;
                    Console.Write(f <= 0.0f ? ".:-=+*#%@"[(int)(f * -8.0f)] : ' ');
                }
                Console.WriteLine();
            }

            Console.Write("爱你宝贝");
            Console.ReadLine();


        }
```

