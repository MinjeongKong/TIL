# 평탄화
[1208.평탄화](https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV139KOaABgCFAYh)

## 풀이
```java
import java.util.*;
import java.io.*;


class Solution
{
	static HashMap<Integer, Integer> hm;
	public static void main(String args[]) throws Exception
	{
		
		Scanner sc = new Scanner(System.in);

		for(int test_case = 1; test_case <= 10; test_case++)
		{
			hm=new HashMap<>();
			int N=sc.nextInt();
			int ma=Integer.MIN_VALUE;
			int mi=Integer.MAX_VALUE;
			for(int i=0;i<100;i++) {
				int input=sc.nextInt();
				hm.put(input, hm.getOrDefault(input, 0)+1);
				ma=Math.max(ma, input);
				mi=Math.min(mi, input);
			}
			while(N>0) {
				hm.put(ma, hm.getOrDefault(ma, 0)-1);
				hm.put(ma-1, hm.getOrDefault(ma-1, 0)+1);
				hm.put(mi, hm.getOrDefault(mi, 0)-1);
				hm.put(mi+1, hm.getOrDefault(mi+1, 0)+1);
				if(hm.get(ma)==0) ma--;
				if(hm.get(mi)==0) mi++;
				N--;
			}
			System.out.println("#"+test_case+" "+(ma-mi));
			
		}
	}
}
```