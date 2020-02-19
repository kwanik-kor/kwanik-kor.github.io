---
layout: post
title:  "[백준BOJ-1753] 최단거리"
subtitle:   "다익스트라(Dijkstra) 알고리즘에 대해 알아보자"
categories: algorithm
tags: dijkstra
comments: true
---

## 개요
>  Dijkstra 알고리즘이란 가중치가 있는 간선이 주어졌을 때, 특정 노드에서 다른 노드들로 이동하는 최단 경로를 구하는 알고리즘이다.

---
문제: [백준 1753: 최단경로](https://www.acmicpc.net/problem/1753 "최단경로")
---

#### 문제
방향그래프가 주어지면 주어진 시작점에서 다른 모든 정점으로의 최단 경로를 구하는 프로그램을 작성하시오. 단, 모든 간선의 가중치는 10 이하의 자연수이다.

#### 입력
첫째 줄에 정점의 개수 V와 간선의 개수 E가 주어진다. (1≤V≤20,000, 1≤E≤300,000) 모든 정점에는 1부터 V까지 번호가 매겨져 있다고 가정한다. 둘째 줄에는 시작 정점의 번호 K(1≤K≤V)가 주어진다. 셋째 줄부터 E개의 줄에 걸쳐 각 간선을 나타내는 세 개의 정수 (u, v, w)가 순서대로 주어진다. 이는 u에서 v로 가는 가중치 w인 간선이 존재한다는 뜻이다. u와 v는 서로 다르며 w는 10 이하의 자연수이다. 서로 다른 두 정점 사이에 여러 개의 간선이 존재할 수도 있음에 유의한다.

#### 출력
첫째 줄부터 V개의 줄에 걸쳐, i번째 줄에 i번 정점으로의 최단 경로의 경로값을 출력한다. 시작점 자신은 0으로 출력하고, 경로가 존재하지 않는 경우에는 INF를 출력하면 된다.

---
#### 풀이
가장 전형적인 Dijkstra 알고리즘의 문제이다. 그렇다면 Dijkstra 알고리즘이 대체 무엇인가?

최단경로를 찾는 알고리즘에는 다익스트라(Dijkstra)를 비롯하여 벨만포드(Bellman-Ford), 플로이드와샬(Floyd-Warshall) 등이 있지만 다른 것들은 추후에 다익스트라와 비교하며 살펴보도록 한다.

다익스트라를 한 마디로 정의하자면 '하나의 노드'에서 '다른 모든 노드'로 가는 최단 거리를 찾아내는 알고리즘이다. 다익스트라의 가장 큰 특징은 바로 '음의 간선'을 가질 수 없다는 것이다. 또한 하나의 최단거리를 구함에 있어 이전에 구한 거리를 활용한다는 점에서 DP(Dynamic Programming)와 같은 특징을 가지고 있다.

알고리즘의 진행 과정은 아래와 같다.
1. 출발 노드를 지정하고 연결되어 있는 각 노드의 최소 비용을 저장한다.
2. 방문하지 않은 노드 중 가장 비용이 적은 노드를 선택한다.
3. 선택한 노드를 거쳐 특정한 노드를 가는 비용을 갱신하며 이를 반복한다.

사실 텍스트만 보았을때는 무슨말인지 잘 이해가 되지 않는다. 이를 명쾌하게 킹갓동빈님께서 설명해주시는 영상을 참고하자.
<https://www.youtube.com/watch?v=611B-9zk2o4>

---
#### 내 코드
```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.util.LinkedList;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	static int V, E, dp[], INF = 10000000;
	static StringTokenizer st;
	static LinkedList<Node> adj[];
	static boolean visited[];
	
	static void dijkstra(int start) {
		PriorityQueue<Node> pq = new PriorityQueue<>();
		dp[start] = 0;
		pq.add(new Node(start, 0));
		while(!pq.isEmpty()) {
			Node now = pq.poll();
			if(visited[now.end]) continue;
			visited[now.end] = true;
			for(Node n : adj[now.end]) {
				//dijkstra 알고리즘의 핵심
				if(dp[n.end] > dp[now.end] + n.value) {
					dp[n.end] = dp[now.end] + n.value;
					pq.add(new Node(n.end, dp[n.end]));
				}
			}
		}
	}
	
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
		st = new StringTokenizer(br.readLine());
		
		//Input 및 초기화 작업
		V = Integer.parseInt(st.nextToken());
		E = Integer.parseInt(st.nextToken());
		adj = new LinkedList[V+1];
		visited = new boolean[V+1];
		dp = new int[V+1];
		for(int i = 1; i<=V; i++){
			dp[i] = INF;
			adj[i] = new LinkedList<Node>();
		}
		int start = Integer.parseInt(br.readLine());
		for(int i = 0; i<E; i++) {
			st = new StringTokenizer(br.readLine());
			int s = Integer.parseInt(st.nextToken());
			int e = Integer.parseInt(st.nextToken());
			int v = Integer.parseInt(st.nextToken());
			adj[s].add(new Node(e, v));
		}
		
		dijkstra(start);
		for(int i = 1; i<=V; i++) {
			bw.write(((dp[i] >= INF)? "INF" : dp[i]) + "\n");
		}
		bw.flush();
		bw.close();
		br.close();
	}
	
	static class Node implements Comparable<Node>{
		int end;
		int value;
		Node(int end, int value){
			this.end = end;
			this.value = value;
		}
		//PriorityQueue에서 사용하기 위해 compareTo method overriding처리
		@Override
		public int compareTo(Node n) { 
			return this.value - n.value;
		}
	}
}
```