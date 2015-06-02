# FriendshipGraph


//Friendship Graph Project
//By Ajeet Mohan //Section 12




import java.io.BufferedReader;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.Reader;
import java.util.ArrayList;
import java.util.Scanner;
import java.util.NoSuchElementException;

class Node {
	boolean seen;
	public Node next;
	int length;

	
	public Node(boolean status, int s, Node m) {
		this.length = -1;
		this.next = null;
		this.seen = false;
	}
	
}



class Vertex {
	String name;
	Neighbor list; 
	String school;
	public Vertex next;
	public int dfsnum;
	public boolean visited;
	public int back;

	
	public Vertex(String name, Neighbor neighbors, String school) {
		this.name = name;
		this.list = neighbors;
		this.school = school;
	}
	
}
class Neighbor {
	public int id;
	public Neighbor next;

	
	public Neighbor(int v, Neighbor n) {
		this.id = v;
		next = n;
	}
	
}
class Queue {

	private Vertex rear;
	private int size;

	public Queue() {
		rear = null;
		size = 0;
	}

	public void enqueue(Vertex v) {
		Vertex newItem = new Vertex(v.name, v.list, v.school);

		if (rear == null) {
			newItem.next = newItem;
		} else {
			newItem.next = rear.next;
			rear.next = newItem;
		}
		size++;
		rear = newItem;
	}
	private void mod_enqueue(Vertex v) {
		Vertex newItem = new Vertex(v.name, v.list, v.school);

		if (rear == null) {
			newItem.next = newItem;
		} else {
			newItem.next = rear.next;
			rear.next = newItem;
		}
		size++;
		rear = newItem;
	}

	public Vertex dequeue() throws NoSuchElementException {
		if (rear == null) {
			throw new NoSuchElementException("queue is empty");
		}
		Vertex data = rear.next;
		if (rear == rear.next) {
			rear = null;
		} else {
			rear.next = rear.next.next;
		}
		size--;
		return data;
	}
	

	public int size() {
		return size;
	}

	public boolean isEmpty() {
		return size == 0;
	}

	public void clear() {
		size = 0;
		rear = null;
	}

	
	public Vertex peek() throws NoSuchElementException {
		if (rear == null) {
			throw new NoSuchElementException("queue is empty");
		}
		return rear.next;
	}
	
}

public class Friends {
	
	static BufferedReader entry = new BufferedReader(new InputStreamReader(System.in));
	public static void main(String[] args) throws IOException{
		Friends a = new Friends();
		 System.out.println("Enter file name: ");
		 String file = entry.readLine(); 
		 BufferedReader br = new BufferedReader(new FileReader(file));
		 a.Graph(file);

		 while(true){
			 int choice = getMenuChoice();
			 
			 if(choice == 1){
				 System.out.println("Enter school name: ");
				 String school = entry.readLine();
				
				 Vertex[] g = a.subGraph(school);
				 if (g == null){
					 continue;
				 }
				 System.out.println("");
				 a.printGraph(g);
				 
			 }
			 else if(choice == 2){
				 System.out.println("Enter first friendz name: ");
				 String firstName = entry.readLine();
				 System.out.println("Enter second friendz name: ");
				 String secondName = entry.readLine();
				 a.shortestPath(firstName, secondName);
				 
			 }
			 else if(choice == 3){
				 System.out.println("Enter name of school: ");
				 String college = entry.readLine();
				 a.findIslands(college);
				 
			 }
			 else if(choice == 4){
				a.getConnectors();
			 }
			 else  if(choice == 5){
				 return;
			 }
		 }
	   }

	   	public static int getMenuChoice() throws IOException{
		System.out.println();
		System.out.println("Menu:");
		System.out.println("1. Find a subgraph");
		System.out.println("2. Get the shorest path");
		System.out.println("3. Find cliques");
		System.out.println("4. Find Connectors");
		System.out.println("5. Quit");
		
		System.out.print("Please choose from 1-5 ");
		String decision = entry.readLine();
		int options = Integer.parseInt(decision);
		
		options=correct(options);
		return options;
		
	}
	   	
	 private static int correct(int options) throws IOException{
		 while(options !=1 && options !=2 && options != 3 && options !=4 & options !=5){
				System.out.print("Pick a number within the range."); 
				String x = entry.readLine();
				options = Integer.parseInt(x);
			}
		 return options;
	 }
	 


	static Vertex[] AL; 
						
	
	private void printGraph(Vertex[] graph){
		
		int n = graph.length;
		System.out.println(n);
		for (int p = 0; p < n; p++) {
			System.out.println(graph[p].name + "|y|" + graph[p].school);
		}//prints out the name of the person with their respective school from the vertex

		for (int i = 0; i < graph.length; i++) {

			Neighbor person = graph[i].list;
			while (person != null) {
				if (person.id < nameID(graph[i].name)) {
					person = person.next;
				} else {
					System.out.println(graph[i].name + "|"
							+ AL[person.id].name);
					person = person.next;
				}
			}
		}
	}
	public void Graph(String file) throws FileNotFoundException {
		BufferedReader br = new BufferedReader(new FileReader(file));

		try {

			int i = Integer.parseInt(br.readLine());
			AL = new Vertex[i];

			for (int v = 0; v < i; v++) {
				String line = br.readLine();

				String[] fields = line.split("\\|");

				if (fields.length == 2) {

					AL[v] = new Vertex(fields[0], null, null);

				}
				if (fields.length == 3) {

					AL[v] = new Vertex(fields[0], null, fields[2]);
				}

			}

			
			String line3;
			while ((line3 = br.readLine()) != null) {

				String[] fields3 = line3.split("\\|");

				int v1 = nameID(fields3[0]);
				int v2 = nameID(fields3[1]);

				AL[v1].list= new Neighbor(v2, AL[v1].list);
				AL[v2].list = new Neighbor(v1, AL[v2].list);

			}
		} catch (Exception e) {
			e.printStackTrace();
		}

		
	}

	
	public static int nameID(String name) {
		for (int v = 0; v < AL.length; v++) {
			if (AL[v].name.equalsIgnoreCase(name)) {
				return v;
			} else {
				continue;
			}
		}

		return -1;//finds the index of the vertex that contains the name and returns -1 if the name does not exist
	}
	
	private int numSchools(Vertex[] AL, int zero, String school){
		int stuff=zero;
		for (int i = 0; i < AL.length; i++) {
			if (AL[i].school != null) {
				if (AL[i].school.equalsIgnoreCase(school)) {
					stuff++;
				} 
			} 
		}
		return stuff;
		
	}

	
	public Vertex[] subGraph(String school) {
		school = school.toLowerCase();
		int n = numSchools(AL, 0, school);
		if (n==0){
			System.out.println("");
			System.out.println("No one in graph goes to " + school);
			return null;
		}
		Vertex[] subgraph = new Vertex[n];
		// creates a vertices array that can store the vertices from the subgraph
		int v = 0;
		for (int i = 0; i < AL.length; i++) {
			if (AL[i].school != null) {
				if (AL[i].school.equalsIgnoreCase(school)) {
					subgraph[v] = new Vertex(AL[i].name, null, school);
					v++;
				} 
			} 
		}
		
		for (int i = 0; i < subgraph.length; i++) {

			int vert = nameID(subgraph[i].name);
			Neighbor person = AL[vert].list;
			Neighbor current = new Neighbor(-1, null);

			while (person != null) {

				if (AL[person.id].school != null) {
					if (AL[person.id].school
							.equalsIgnoreCase(school)) {
						
						if (subgraph[i].list == null) {
							Neighbor neig = new Neighbor(person.id, null);
							current = neig;
							subgraph[i].list = current;
							person = person.next;
						} else {
							current.next = new Neighbor(person.id, null);
							current = current.next;
							person = person.next;
						}
					} else {
						person = person.next;
					}
				} else {
					person = person.next;
				}

			}
		}

		return subgraph;
	}

	public void findIslands(String college) {
		college = college.toLowerCase();
		
		Vertex[] schoolCommunity = subGraph(college);
		if (schoolCommunity == null){
			return;
		}
		Vertex[] clique = new Vertex[schoolCommunity.length];
		ArrayList<Vertex[]> allCliques = new ArrayList<Vertex[]>();
		System.out.println("");

		int num = 0;
		boolean again = true;
		while (again == true) {
			for (int i = 0; i < schoolCommunity.length; i++) {

				if (num == 0) {
					if (schoolCommunity[i] != null) {

						clique[num] = schoolCommunity[i];

						num = 1;
						Neighbor person = schoolCommunity[i].list;
						while (person != null) {
							clique[num] = AL[person.id];

							num++;
							person = person.next;
						}
						continue; 
					} 
				} else {

					for (int k = 0; k < clique.length; k++) {
						if (clique[k] == null) {
							continue;
						} else {
							if (clique[k] != null && schoolCommunity[i] != null) {
								if (clique[k].name.equals(schoolCommunity[i].name)) {
									Neighbor person = schoolCommunity[i].list;

									boolean there = false;
									while (person != null) {

										for (int d = 0; d < clique.length; d++) {
											if (clique[d] != null) {
												if (AL[person.id].name
														.equalsIgnoreCase(clique[d].name)) {

													there = true;
													continue;
												} 
											} 
										}
										if (there == false) {

											clique[num] = AL[person.id];

											num++;
											person = person.next;
										}
										if (there == true) {

											person = person.next;
										}

									}
								} else {

									continue;
								}
							} else {
								continue;
							}
						}
					}

				}
			}
			int size =numCliques(clique, 0);

			boolean[] isClique = new boolean[schoolCommunity.length];

			for (int l = 0; l < schoolCommunity.length; l++) {
				for (int n = 0; n < clique.length; n++) {
					if (clique[n] != null && schoolCommunity[l] != null) {
						if (clique[n].name.toLowerCase()
								.equals(schoolCommunity[l].name.toLowerCase())) {

							isClique[l] = true;
						} else {
							continue;
						}
					} else {
						continue;
					}
				}
			}

			int x=numCliques(clique, 0);
			allCliques.add(clique);
			clique = new Vertex[schoolCommunity.length];
			Vertex[] v2 = new Vertex[schoolCommunity.length - x];

			int vert = 0;
			for (int w = 0; w < schoolCommunity.length; w++) {

				if (isClique[w] != true && schoolCommunity[w] != null) {

					v2[vert] = schoolCommunity[w];
					vert++;
				} else {
					continue;
				}

			}

			schoolCommunity = v2;
			num = 0;

			if (schoolCommunity.length == 0) {

				again = false;

			} else {

				again = true;
			}

		}
		
		int v = 1;
		for (int i = 0; i < allCliques.size(); i++) {

			System.out.println("");
			System.out.println("Clique " + v + ":");
			System.out.println("");
			v++;
			int size = 0;
			for (int j = 0; j < allCliques.get(i).length; j++) {
				if (allCliques.get(i)[j] != null) {
					size++;
				} else {
					continue;
				}
			}

			System.out.println(size);
			for (int k = 0; k < allCliques.get(i).length; k++) {
				if (allCliques.get(i)[k] != null) {
					System.out.println(allCliques.get(i)[k].name + "|y|"
							+ allCliques.get(i)[k].school);
				} else {
					continue;
				}
			}
	for (int p = 0; p < allCliques.get(i).length; p++) {
		if (allCliques.get(i)[p] != null) {
			Neighbor person = allCliques.get(i)[p].list;
			while (person != null) {
				if (person.id < nameID(allCliques.get(i)[p].name)) {
					person = person.next;
				} else {
					if (allCliques.get(i)[p] != null) {
						boolean there = false;
						for (int y = 0; y < allCliques.get(i).length; y++) {
							if (allCliques.get(i)[y] != null) {
								if (AL[person.id].name
										.equals(allCliques.get(i)[y].name)) {
									there = true;
								} else {
									continue;
								}
							}
						}
							if (there == true) {
							System.out
							.println(allCliques.get(i)[p].name
										+ "|"
										+ AL[person.id].name);
							}
						} 
						person = person.next;
					}
				}
			} 
		}
	}
}
	private int numCliques(Vertex[] a, int zero){
		int s=zero;
		for (int b = 0; b < a.length; b++) {
			if (a[b] != null) {
				s++;
			} 
		}
		return s;
	}
	
	public void shortestPath(String first, String last) {

		
		if (first == null || last == null) {
			System.out.println("One or more input was invalid.");
		}
		Vertex firstFriend = null;
		

	for (int i = 0; i < AL.length; i++) {
		if (AL[i].name.toLowerCase().equals(first.toLowerCase())) {
				firstFriend = AL[i];
			} 
	}

	if (firstFriend == null) {
			System.out.println("The: " + start
					+ " could not be found. Please put another input.");
			return;
		} else {
			boolean s = false;
			for(int i = 0; i<AL.length; i++){
				if(AL[i].name.equalsIgnoreCase(last)){
					s = true;
				}
				
			}
			
			if (s == false){
				System.out.println(last + " is not in the list."); //ending input is not in the list
				return;
			}
			
			Neighbor user = firstFriend.list;
			
			while (user != null) {
				if (AL[user.id].name.equalsIgnoreCase(last)) {
					System.out.println(first + " is friends with "
							+ last);
					return;
				} else {
					user = user.next;
				}
			}
			user = firstFriend.list;

		}

		Queue q = new Queue();

	
		first = first.toLowerCase();
		last = last.toLowerCase();

		int lastIndex;
		Vertex vert;
		Neighbor n;

		
		Node[] visited = new Node[AL.length];
		Vertex[] path = new Vertex[AL.length];

		for (int i = 0; i < visited.length; i++) {
			visited[i] = new Node(false, -1, null);
		}
		lastIndex = nameID(last);
		visited[lastIndex].seen = true;
		q.enqueue(AL[lastIndex]);

		while (!q.isEmpty()) {
			vert = q.dequeue();
			n = vert.list;

			while (n != null) {
				q=isSeen(n, q, visited, path, vert);
				n = n.next;
			}
		}
	
		if (visited[nameID(first)].length == -1 || first.equals(last)) {
			System.out.println("You cannot get from " + first + " to "
					+ last);
		} else {
			int begin = nameID(first);
			while (!last.equals(AL[begin].name)) {

				System.out.print(AL[begin].name + "--");

				begin = nameID(path[begin].name);
			}
			System.out.println(last);
		}
	}
	
	ArrayList<String> connectors = new ArrayList<String>();
	int dfsn = 0;
	int start = 0;
	private Queue isSeen(Neighbor stuff, Queue queue, Node[] visited, Vertex[] path, Vertex vert){
		if (visited[stuff.id].seen == false) {
			visited[stuff.id].length = 1;
			path[stuff.id] = vert;
			visited[stuff.id].seen = true;

			queue.enqueue(AL[stuff.id]);

		}
		return queue;
	}
	private void adjustNumbers(Vertex v) {//this method adjusts the back and dfs numbers according to the stated algorithm

		
		System.out.println("Initial Vertex: " + v.name);
		Neighbor f = v.list;
		
		dfsn++;
		v.dfsnum = dfsn;
		v.back = dfsn;
		v.visited = true;

		for(f = v.list; f!=null; f = f.next){
			
			Vertex w = AL[f.id];
			System.out.println(w.name+ "comes after "+v.name);
			if (!w.visited){
				System.out.println("Perform a DFS from "+w.name);
				adjustNumbers(w);
				if (nameID(v.name)!= start && v.dfsnum <= w.back){
					if(!connectors.contains(v.name)){
						System.out.println("Adding "+v.name+" to the connectors list.");
						connectors.add(v.name);
					
					}
				}
				if(v.dfsnum > w.back){
					v.back = Math.min(v.back, w.back);
				}
			}
			else{
				System.out.println(w.name+" was visited already.");
				v.back = Math.min(v.back, w.dfsnum);
				
			}
		}
	}

	private void firstDFS(Vertex v) {

		System.out.println("");
		System.out.println("Starting Vertex: " + v.name);
		Neighbor c = v.list;
		
		dfsn++;
		v.dfsnum = dfsn;
		v.back = dfsn;
		v.visited = true;

		for(c = v.list; c!=null; c = c.next){
			
			Vertex w = AL[c.id];
			System.out.println(v.name+ "'s friend "+w.name+" came next");
			if (!w.visited){
				System.out.println("Recursive DFS from "+w.name);
				adjustNumbers(w);
				if (nameID(v.name)!= start && v.dfsnum <= w.back){
					if(!connectors.contains(v.name)){
						System.out.println("Adding "+v.name+" to the connectors list.");
						connectors.add(v.name);
					
					}
				}
			}}}
			
	private void change(Vertex[] list){
		for(int i = 0; i < list.length; i++) {

			Vertex v = list[i];
			
			
			if(!v.visited) {
			
			
				dfsn = 0;
				start = i;
				adjustNumbers(v);
			}else{
				continue;
			}
		}
	}
		
	public void getConnectors() {

		change(AL);
		System.out.println("");
		System.out.println("The connectors are: ");
		System.out.println("");
		for (int i = 0; i<connectors.size(); i++){
			if (i==connectors.size()-1){
				System.out.print(connectors.get(i));
			}else{
				System.out.print(connectors.get(i)+",");}
		}
		System.out.println("");
	}	
	
}







