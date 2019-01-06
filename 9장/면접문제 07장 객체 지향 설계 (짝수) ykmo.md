## 면접문제 07장 객체 지향 설계 (짝수) ykmo


#### 7.2 콜센터
고객 응대 담당자, 관리자, 감독관 이렇게 세 부류의 직원들로 구성된 콜 센터가 있다고 하자. 콜 센터로 오는 전화는 먼저 상담이 가능한 고객 응대 담당자로 연결돼야 한다. 고객 응대 담당자가 처리할 수 없는 전화는 관리자로 연결되고, 관리자가 처리할 수 없는 전화는 다시 감독관에게 연결된다. 이 문제를 풀기 윈한 자료구조를 설계하라. 응대 가능한 첫 번째 직원에게 전화를 연결시키는 dispatchCall() 메서드를 구현하라.



개요

> Call : 전화를 나타내는 클래스
- 현재 Call 객체 상태를 나타냄

> RingPool : 걸려온 전화를 직원에 넘기는 클래스
- Call 객체를 받을수 있는 Staff를 찾아 전달한다.

> Staff : 직원을 나타내는 클래스. Call 객체를 가진다.
- 전달받은 Call객체를 처리한다.

> Call의 흐름 : Call 발생 - > RingPool -> Staff 에게 전달됨

> 필요한 개선점 : Call 발생 로직, Pool을 통합관리하는 PoolManager
```cs


///Programs.cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace RingRing
{
    class Program
    {
        static List<RingPool> poolList = new List<RingPool>();

        static void Main(string[] args)
        {
            //고객 응대 담당자 풀 생성
            RingPool employeePool = new RingPool();
            employeePool.InitPool(5);
            poolList.Add(employeePool);

            //관리자 풀 생성
            RingPool managerPool = new RingPool();
            managerPool.InitPool(5);
            poolList.Add(managerPool);

            //감동관 풀 생성
            RingPool directorPool = new RingPool();
            directorPool.InitPool(3);
            poolList.Add(directorPool);

            DispatchCall(new Call());
            DispatchCall(new Call());
            DispatchCall(new Call());

        }

        //Call을 Pool에 전달함
        public static void DispatchCall(Call call)
        {
            foreach(RingPool pool in poolList)
            {
                if (pool.AssignCall(call))
                {
                    //배정이 완료되었으면 종료
                    return;
                }
            }
        }
    }
}



///RingRing.cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace RingRing
{

    class RingPool
    {

        private List<Staff> staffList;
        private int Priority;


        public void InitPool(int maxStaffCount)
        {
            staffList = new List<Staff>(new Staff[maxStaffCount]);
        }



        /// <summary>
        /// 전화를 받을수 있는 Staff를 찾아 배정한다.
        /// </summary>
        /// <param name="call"></param>
        /// <returns></returns>
        public bool AssignCall(Call call)
        {
            if (CheckAvailableStaff())
            {
                int staffIndex = GetAvailableStaffIndex();
                if (staffIndex != -1)
                {
                    staffList[staffIndex].AssignCall(call);
                    return true;
                }

            }

            return false;
        }



        /// <summary>
        /// 전화를 받을 수 있는 Staff를 찾는다.
        /// </summary>
        /// <returns></returns>
        private bool CheckAvailableStaff()
        {
            foreach (Staff staff in staffList)
            {
                if (!staff.IsCallAssigned())
                {
                    return true;
                }
            }


            return false;
        }


        //전화를 받을수 있는 상태인 스태프를 찾음
        private int GetAvailableStaffIndex()
        {
            int index = 0;
            foreach (Staff staff in staffList)
            {
                if (staff.IsCallAssigned())
                {
                    return index;
                }
                index++;
            }

            return -1;
        }
    }
}



///Staff.cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace RingRing
{
    class Staff
    {
        private Call call;


        public void AssignCall(Call call)
        {
            this.call = call;
        }

        public bool IsCallAssigned()
        {
            return call.IsProceeding();
            
        }
    }
}


///Call.cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace RingRing
{
    class Call
    {
        /// <summary>
        /// 전화가 현재 진행중인지를 의미
        /// </summary>
        private bool proceeding = false;


        /// <summary>
        /// 전화를 받았을 때
        /// </summary>
        public void SetCallProceeding()
        {
            proceeding = true;
        }


        /// <summary>
        /// 전화를 끊었을때
        /// </summary>
        public void SetCallFinish()
        {
            proceeding = false;
        }


        /// <summary>
        /// 현재 전화상태를 리턴
        /// </summary>
        /// <returns></returns>
        public bool IsProceeding()
        {
            return proceeding;
        }

  
    }
}


```


#### 7.4 주차장

객체 지향 원칙에 따라 주차장(parking lot)을 설계하라.


- 가정 
    - 이마트 주차장처럼 층 구조로된 주차장을 상정
    - 차는 한번에 하나씩 들어가고 나올수 있다.




> Car : 자동차를 나타내는 객체

> Floor : 주차장의 한개의 층

> FloorManager : 층을 관리하는 관리 객체(싱글턴 객체로 생성함)


```cs

///Program
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ParkingLot
{
    class Program
    {


        static void Main(string[] args)
        {
            FloorManager floorManager = new FloorManager();

            //Floor 객체 생성 및 각 층마다 최대로 수용가능한 차량댓수 설정
            floorManager.EnrollFloor(new Floor(10, 1));
            floorManager.EnrollFloor(new Floor(25, 2));
            floorManager.EnrollFloor(new Floor(35, 3));
            floorManager.EnrollFloor(new Floor(22, 4));
            floorManager.EnrollFloor(new Floor(23, 5));

            floorManager.PushCar(new Car(1.5f, "35바2459"));
            floorManager.PushCar(new Car(1.5f, "15호8422"));
            floorManager.PushCar(new Car(1.5f, "35모1875"));
        }
    }
}


///FloorManager.cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ParkingLot
{
    class FloorManager
    {

        private static FloorManager instance = null;

        List<Floor> floorList = new List<Floor>();

        //Thread-safe 하지 않음!
        public static FloorManager Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new FloorManager();
                }
                return instance;
            }
        }

        /// <summary>
        /// floor를 등록함
        /// </summary>
        /// <param name="floor"></param>
        public void EnrollFloor(Floor floor)
        {
            floorList.Add(floor);
        }

        
        /// <summary>
        /// 차를 주차시킴
        /// </summary>
        /// <param name="car"></param>
        public void PushCar(Car car)
        {
            foreach(Floor floor in floorList)
            {
                int carLimit = floor.GetMAX_AvailableCar();

                if (floor.GetCarCount() <= carLimit)
                {
                    floor.ParkCar(car);
                    return;
                }
            }
        }
    }
}



///Floor.cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ParkingLot
{

    class Floor
    {
        private int MAX_AvailableCar;
        private HashSet<Car> carSet = new HashSet<Car>();
        private int floorNumber;

        public Floor(int MAX_AvailableCar, int floorNumber)
        {
            this.MAX_AvailableCar = MAX_AvailableCar;
            this.floorNumber = floorNumber;
        }
        public int GetCarCount()
        {
            return carSet.Count;
        }

        public int GetMAX_AvailableCar()
        {
            return MAX_AvailableCar;
        }

        public void ParkCar(Car car)
        {
            if (carSet.Count <= MAX_AvailableCar)
            {
                carSet.Add(car);
            }

        }

        /// <summary>
        /// 주차장에서 차를 뺌
        /// </summary>
        /// <param name="car"></param>
        public void PullCar(Car car)
        {
            carSet.Remove(car);
        }

    }
}


///Car.cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ParkingLot
{
    class Car
    {
        float height;
        string carNumber;

        public Car(float height, string carNumber)
        {
            this.height = height;
            this.carNumber = carNumber;
        }

        public float GetCarheight()
        {
            return height;
        }

        public string SetCarNumber()
        {
            return carNumber;
        }
    }
}


```


#### 7.6 직소
N X N 크기의 직소(jigsaw)퍼즐을 구현하라. 자료구조를 설계하고, 퍼즐을 푸는 알고리즘을 설명하라. 두 조각의 퍼즐 모서리가 주어졌을 때 그들이 들어맞는지 아닌지 알려주는 fitsWith 메서드는 제공된다.

```java
import java.util.HashMap;
import java.util.LinkedList;

public class Main {

    public enum Orientation {
        LEFT, TOP, RIGHT, BOTTOM;


        //방향의 반대 방향을 구함
        public Orientation getOpposite(){
            switch (this){
                case LEFT: return RIGHT;
                case RIGHT: return LEFT;
                case TOP: return BOTTOM;
                case BOTTOM: return TOP;
                default: return null;
            }
        }
    }

    public enum Shape {
        INNER, OUTER, FLAT;

        //모양의 반대를 구함
        public Shape getOpposite() {
            switch (this) {
                case INNER:
                    return OUTER;
                case OUTER:
                    return INNER;
                default:
                    return null;
            }

        }
    }


        public class Puzzle {
            private LinkedList pieces;
            private Piece[][] solution;
            private int size;

            public Puzzle(int size, LinkedList pieces){
                //...
            }

            //퍼즐 조각을 적절하게 회전시킨 뒤 내려놓고, 리스트에서 삭제한다.
            private void setEdgeInSolution(LinkedList pieces, Edge edge, int row, int column, Orientation orientation){
                Piece piece = edge.getParentPiece();
                piece.setEdgeAsOrientation(edge, orientation);
                piece.remove(piece);
                solution[row][column] = piece;
            }

            //알맞은 퍼즐 조각을 piecesToSearch에서 찾은 뒤 해당 행, 열에 넣는다
            private boolean fixNextEdge(LinkedList piecesToSearch, int row, int column){

                //맨 처음 조각을 처리함
                if (row == 0 && column == 0) {
                    Piece p = piecesToSearch.remove();
                    orientTopLeftCorner(p);
                    solution[0][0]=p;

                } else{
                    Piece pieceToMatch = column == 0 ? solution[row - 1][0]
                            :solution[row][column-1];

                    Orientation orientationToMatch = column == 0 ? Orientation.BOTTOM:
                            Orientation.RIGHT;
                    Edge edgeToMatch = pieceToMatch.getEdgeWithOrientation(orientationToMatch);

                    //들어맞는 모서리 구하기
                    Edge edge = getMatchingEdge(edgeToMatch, piecesToSearch);
                    if(edge == null) return false;

                    //퍼즐 조각과 모서리를 삽입
                    Orientation orientation = orientationToMatch.getOpposite();
                    setEdgeInSolution(piecesToSearch, edge, row, column, orientation);
                }
                return true;
            }


            public boolean solve() {

                //퍼즐 조각들을 나누어 놓기
                LinkedList cornerPieces = new LinkedList();
                LinkedList borderPieces = new LinkedList();
                LinkedList insidePieces = new LinkedList();
                groupPieces(cornerPieces, borderPieces, insidePieces);


                //퍼즐을 살펴보면서 이전 조각과 들어맞는 퍼즐 조각 찾기
                solution = new Piece[size][size];
                for(int row = 0; row < size; row++){
                    for(int column = 0; column <size; column++){
                        LinkedList piecesToSearch = getPieceListToSearch(cornerPieces, borderPieces, insidePieces, row, column);
                        if(!fixNextEdge(piecesToSearch, row, column)){
                            return false;
                        }
                    }
                }

                return true;
            }
        }




    public class Piece{
        private HashMap edges = new HashMap();

        public Piece(Edge[] edgeList){
            //...
        }

        public void rotateEdgesBy(int numberRotations){
            //...
        }

        public boolean isCornet() {
            //...
        }
        public  boolean isBorder(){
            //..
        }
    }

    public class Edge {
        private Shape shape;
        private Piece parentPiece;
        public Edge(Shape shape){
            //...
        }
        public boolean fitsWith(Edge edge){
            //..
        }
    }



    public static void main(String[] args) {

    }
}


```


#### 7.8 오셀로
오셀로(Othello) 게임 규칙은 이러하다. 오셀로 말의 한쪽 면은 흰색, 반대 면은 검은색으로 칠해져 있다. 상대편 말에게 왼쪽과 오른쪽, 또는 위와 아래가 포위된 말은 색상을 뒤집어 상대편 말이 된것으로 표시한다. 여러분은 여러분 차례에서 적어도 상대편 말 한개를 획득해야 한다. 더 이상 진행이 불가능해지면 게임은 종료된다. 가장 많은 말을 획득한 사람이 승자가 된다. 이 게임을 객체 지향적으로 설계하라.


```java
public class Main {


    public enum Direction {
        left, right, up, down
    }

    public enum Color{
        White, Black
    }

    public class Game{
        private Player[] players;
        private static Game instance;
        private Board board;
        private final int ROWS = 10;
        private final int COLUMNS = 10;


        private Game(){
            board = new Board(ROWS, COLUMNS);
            players = new Board.Player[2];
            players[0] = new Player(Color.Black);
            players[1] = new Player(Color.White);

        }


        public static Game getInstance(){
            if(instance == null) instance = new Game();
            return instance;
        }

        public Board getBoard(){
            return board;
        }
    }


    public class Board {
        private int blackCount = 0;
        private int whiteCount = 0;
        private Piece[][] board;
        public Board(int rows, int columns){
            board = new Piece[rows][columns];
        }

        public void initialize(){
            //중앙에 검정말과 흰색 말을 놓아 초기화한다
        }

        //행 (row, 열 column)에 색상이 color인 말을 배치한다.
        //제대로 배치됐으면 true를 반환한다.
        public boolean placeColor(int row, int column, Color color){
            //...
        }

        //(행 row, 열 column)부터 시작해서 d 방향으로 진행하면서 말을 뒤집는다.
        private int flipSection(int row, int column, Color color, Direction d){
            //...
        }

        public int getScoreForColor(Color c){
            if(c==Color.Black) {return blackCount;}
            else {return whiteCount;}
        }

        //색상이 newColor인 새로운 말들을 newPieces개 만큼 더 놓아 게임판을 갱신한다.
        //반되되는 색상의 점수는 감소시킨다.
        public void updateScore(Color newColor, int newPieces){

        }

        public class Pieces {
            private Color color;
            public Piece(Color c) {color = c;}
            public void flip(){
                if(color == Color.Black) color = Color.White;
                else color = Color.Black;
            }
            public Color getColor() {return color;}
        }



    }
    public class Player {
        private Color color;
        public Player(Color c) {color = c;}

        public int getScore(){}
        public boolean playerPiece(int r, int c){
            return Game.getInstance().getBoard().placeColor(r, c, color);
        }

        public Color getColor() { return color;}
    }

    public static void main(String[] args) {




    }
}



```


#### 7.10 지뢰찾기

텍스트 기반의 지뢰찾기(Minesweeper)게임을 설계하고 구현하라. 지뢰찾기는 NxN 격자판에 숨겨진 B개의 지뢰를 찾는 고전적인 컴퓨터 게임이다. 지뢰가 없는 셀(cell)에는 아무것도 적혀 있지 않거나 인접한 여덟 방향에 숨겨진 지뢰의 개수가 적혀 있다. 플레이어는 각 셀을 확인해 볼 수 있는데, 확인한 셀에 지뢰가 있다면 그 즉시 게임에서 진다. 확인한 셀에 숫자가 적혀 있다면 그 값이 공개된다. 해당 셀이 비어있다면 인접한 비어 있는 셀(숫자로 둘러싸인 셀을 만나기 전까지)모두 공개된다. 지뢰가 없는 모든 셀을 전부 공개된 상태로 바꿔 놓으면 플레이어가 이긴다. 지뢰가 있을 것 같은 위치에 깃발을 꽂아 표시해 둘 수 있는데, 깃발을 꽂는 것은 게임에 아무런 영향을 미치지 않는다. 실수로 잘못 클릭하는 상황을 방지하기 위한 기능일 뿐이다.




#### 7.12 해시테이블
체인(chain 즉 연결리스트)을 사용해 충돌을 해결하는 해시테이블을 설계하고 구현하라.