#using System;
#using System.Threading;
#using static System.Console;

#namespace Snake_Game
{
    class Program
    {
        class Start
        {
            public static int x;
            public static int y;
            public static void Init()
            {
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine("                #####   #    #     #      #  #  #####        ");
                Console.WriteLine("                #       #   ##    # #     # #   #              ");
                Console.WriteLine("                #####   #  # #   #####    ##    #####          ");
                Console.WriteLine("                    #   # #  #  #     #   # #   #                 ");
                Console.WriteLine("                #####   ##   # #       #  #  #  #####         ");
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine();
            }
            public static int MenuDraw()
            {

                x = 30;
                y = 15;

                Console.SetCursorPosition(x, y);
                Console.WriteLine(">  Start");
                Console.SetCursorPosition(x, y + 1);
                Console.WriteLine("   information");
                Console.SetCursorPosition(x, y + 2);
                Console.WriteLine("   exit  ");

                while (true)
                {
                    ConsoleKeyInfo info = Console.ReadKey();
                    switch (info.Key)
                    {
                        case ConsoleKey.UpArrow:
                            if (y > 15)
                            {
                                Console.SetCursorPosition(x, y);
                                Console.Write(" ");
                                Console.SetCursorPosition(x, --y);
                                Console.Write(">");
                            }
                            break;
                        case ConsoleKey.DownArrow:
                            if (y < 17)
                            {
                                Console.SetCursorPosition(x, y);
                                Console.Write(" ");
                                Console.SetCursorPosition(x, ++y);
                                Console.Write(">");
                            }
                            break;
                        case ConsoleKey.Enter:
                            return y - 15;
                    }
                }
            }

            public static void InfoDraw()
            {
                Console.Clear();
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine("                                [ 조작법 ]                     ");
                Console.WriteLine();
                Console.WriteLine("                              이 동 : 방향키                    ");
                Console.WriteLine();
                Console.WriteLine("                              선 택 : ENTER                    ");
                Console.WriteLine();
                Console.WriteLine("                   ENTER를 누르면 메인화면으로 이동합니다.          ");

                while (true)
                {
                    ConsoleKeyInfo info = Console.ReadKey();
                    if (info.Key == ConsoleKey.Enter)
                        break;
                }
            }

            static void Main(string[] args)
            {
               
                bool gamestate = true;
                bool isWallHit = false;
                decimal gameSpeed = 150m;

                bool isFoodEaten = false;
                int foodEaten = 0;

                CursorVisible = false;

                Random rand = new Random();
                
               
                bool state = true;

                while (state)
                {
                    Start.Init();
                    int menuCode = Start.MenuDraw();
                    if (menuCode == 0)
                    {
                        int a = rand.Next(2, 47);
                        int b = rand.Next(2, 23);
                        int[] xPosition = new int[50];
                        xPosition[0] = a;
                        int[] yPosition = new int[50];
                        yPosition[0] = b;
                        Clear();
                        #region Setup
                        paintSnake(foodEaten, xPosition, yPosition, out xPosition, out yPosition);

                        int foodX = rand.Next(1, 47);
                        int foodY = rand.Next(1, 23);
                        /* while (xPosition[0] != foodX && yPosition[0] != foodY)
                         {*/

                        setFoodPosition(rand, out foodX, out foodY);
                        paintFood(foodX, foodY/*, xPosition[0], yPosition[0]*/);
                        //break; 
                        //}


                        buildWall();
                        #endregion
                        ConsoleKey c = ReadKey().Key;
                        do
                        {
                            switch (c)
                            {
                                case ConsoleKey.UpArrow:
                                    SetCursorPosition(xPosition[0], yPosition[0]);
                                    Write(" ");
                                    yPosition[0]--;
                                    break;
                                case ConsoleKey.DownArrow:
                                    SetCursorPosition(xPosition[0], yPosition[0]);
                                    Write(" ");
                                    yPosition[0]++;
                                    break;
                                case ConsoleKey.LeftArrow:
                                    SetCursorPosition(xPosition[0], yPosition[0]);
                                    Write(" ");
                                    xPosition[0]--;
                                    break;
                                case ConsoleKey.RightArrow:
                                    SetCursorPosition(xPosition[0], yPosition[0]);
                                    Write(" ");
                                    xPosition[0]++;
                                    break;
                            }
                            //뱀 몸통 그리기
                            paintSnake(foodEaten, xPosition, yPosition, out xPosition, out yPosition);

                            isWallHit = DidSnakeHitWall(xPosition[0], yPosition[0]);

                            if (isWallHit)
                            {
                                gamestate = false;
                                SetCursorPosition(15, 10);
                                WriteLine("뱀이 벽에 부딪혔습니다.");
                                ForegroundColor = ConsoleColor.White;
                                SetCursorPosition(15, 15);
                                Write("Your Score is " + foodEaten * 100);
                            }
                            //Food가 먹혔는지 여부
                            isFoodEaten = detectFood(xPosition[0], yPosition[0], foodX, foodY);
                            //Food가 먹혔을 때
                            if (isFoodEaten)
                            {
                                setFoodPosition(rand, out foodX, out foodY);
                                paintFood(foodX, foodY/*, xPosition[0], yPosition[0]*/);
                                foodEaten++;
                                gameSpeed += 0.925m;
                            }
                            if (KeyAvailable)
                                c = ReadKey().Key;
                            Thread.Sleep(Convert.ToInt32(gameSpeed));
                        } while (gamestate);
                        break;
                    }
                    else if (menuCode == 1)
                    {
                        Start.InfoDraw();
                    }
                    else if (menuCode == 2)
                    {
                        state = false;
                    }
                    Clear();
                }
            }


            private static void paintSnake(int foodEaten, int[] xPositionIn, int[] yPositionIn, out int[] xPositionOut, out int[] yPositionOut)
            {
                SetCursorPosition(xPositionIn[0], yPositionIn[0]);
                ForegroundColor = ConsoleColor.Yellow;
                WriteLine("@");
                for (int i = 1; i < foodEaten + 1; i++)
                {
                    SetCursorPosition(xPositionIn[i], yPositionIn[i]);
                    ForegroundColor = ConsoleColor.Yellow;
                    WriteLine("o");
                }
                SetCursorPosition(xPositionIn[foodEaten + 1], yPositionIn[foodEaten + 1]);
                Write(" ");
                for (int i = foodEaten + 1; i > 0; i--)
                {
                    xPositionIn[i] = xPositionIn[i - 1];
                    yPositionIn[i] = yPositionIn[i - 1];
                }
                xPositionOut = xPositionIn;
                yPositionOut = yPositionIn;
            }
            private static bool detectFood(int xPosition, int yPosition, int foodX, int foodY)
            {
                if (xPosition == foodX && yPosition == foodY)
                    return true;
                else
                    return false;
            }
            private static void paintFood(int foodX, int foodY/*,int xPosition,int yPosition*/)
            {
                //if (xPosition != foodX && yPosition != foodY)
                //{
                SetCursorPosition(foodX, foodY);
                ForegroundColor = ConsoleColor.Red;
                Write("*");
                // }
            }

            private static void setFoodPosition(Random rand, out int foodX, out int foodY)
            {
                foodX = rand.Next(2, 47);
                foodY = rand.Next(2, 23);
            }
            private static bool DidSnakeHitWall(int xPosition, int yPosition)
            {
                if (xPosition == 0 || xPosition == 49 || yPosition == 0 || yPosition == 24)
                    return true;
                else
                    return false;
            }

            private static void buildWall()
            {
                for (int i = 0; i < 25; i++)
                {
                    ForegroundColor = ConsoleColor.White;
                    SetCursorPosition(0, i);
                    Write("#");
                    SetCursorPosition(49, i);
                    Write("#");
                }
                for (int i = 0; i < 50; i++)
                {
                    ForegroundColor = ConsoleColor.White;
                    SetCursorPosition(i, 0);
                    Write("#");
                    SetCursorPosition(i, 24);
                    Write("#");
                }
            }
        }
    }
}
    

