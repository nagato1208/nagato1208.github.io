---
title: OOD-lintcode-709-Restaurant OO Design
date: 2019-09-10 16:26:02
categories: Lintcode
tags: ['OOD']
---

## 描述
设计一个餐馆:

- 不考虑预订座位

- 不考虑订外卖

- 餐馆的桌子有不同大小

- 餐馆会优先选择适合当前Party最小的空桌

- 请实现`Restaurant Class`, hints: `findTable()`, `takeOrder()`, `checkOut()`.


## 思路

感觉OOD主要就是脑洞啊...尽量往实际场景中靠拢.

需要考虑实现的对象:

- `Meal`
    -`price` (lunch or dinner)
    -`available_time`
- `Order`
    - `List<Meal> meals`
    - `mergeOrder()` (客人要求加菜)
    - `getMeals()`
    - `getBill()`
- `Table`
    - `size`(`capacity`)
    - `available`
    - `getOrder` (一张桌子对应一个订单)
    - `setOrder`
- `Party`
    - `size`
    
实现的方法:
- `findTable()` 返回最小的available table
- `checkOut(Table t)`
- `takeOrder(Table t, Order o)`







## 代码
```java
class NoTableException extends Exception{

	public NoTableException(Party p)
	{
		super("No table available for party size: " + p.getSize());
	}
}

class Meal {
	private float price;
	
	public Meal(float price)
	{
		this.price = price;
	}
	
	public float getPrice()
	{
		return this.price;
	}
}

class Order {
	private List<Meal> meals;
	
	public Order()
	{
		meals = new ArrayList<Meal>();
	}
	
	public List<Meal> getMeals()
	{
		return meals;
	}
	
	public void mergeOrder(Order order)
	{
		if(order != null)
		{
			for(Meal meal : order.getMeals())
			{
				meals.add(meal);
			}
		}
	}
	
	public float getBill()
	{
		int bill = 0;
		for(Meal meal : meals)
		{
			bill += meal.getPrice();
		}
		return bill;
	}
}

class Party {
	private int size;
	
	public Party(int size)
	{
		this.size = size;
	}
	
	public int getSize()
	{
		return this.size;
	}
}

class Table implements Comparable<Table>{
	private int capacity;
	private boolean available;
	private Order order;
	
	public Table(int capacity)
	{
		this.capacity = capacity;
		available = true;
		order = null;
	}
	
	public int getCapacity()
	{
		return this.capacity;
	}
	
	public boolean isAvailable()
	{
		return this.available;
	}
	
	public void markAvailable()
	{
		this.available = true;
	}
	
	public void markUnavailable()
	{
		this.available = false;
	}
	
	public Order getCurrentOrder()
	{
		return this.order;
	}
	
	public void setOrder(Order o)
	{
		if(order == null)
		{
			this.order = o;
		}
		else 
		{
			if(o != null)
			{
				this.order.mergeOrder(o);
			} else {
				this.order = o;
			}
		}
	}

	@Override
	public int compareTo(Table compareTable) {
		// TODO Auto-generated method stub
		return this.capacity - compareTable.getCapacity();
	}
}

public class Restaurant {
	private List<Table> tables;
	private List<Meal> menu;
	
	public Restaurant()
	{
		// Write your code here
		tables = new ArrayList<>();
		menu = new ArrayList<>();
	}
	
	public void findTable(Party p) throws NoTableException
	{
		// Write your code here
		
		for (Table t : tables) {
		    if (t.isAvailable() && t.getCapacity() >= p.getSize()) {
		        t.markUnavailable();
		        return;
		    }
		}
		throw new NoTableException(p);
	}
	
	public void takeOrder(Table t, Order o)
	{
		// Write your code here
		t.setOrder(o);
	}
	
	public float checkOut(Table t)
	{
		// Write your code here
		t.markAvailable();
		float amount = 0;
		if (t.getCurrentOrder() != null) {
		    amount = t.getCurrentOrder().getBill();
		    t.setOrder(null);
		}
		return amount;
	}
	
	public List<Meal> getMenu()
	{
		return menu;
	}
	
	public void addTable(Table t)
	{
		// Write your code here
		tables.add(t);
		Collections.sort(tables);
	}
	
	public String restaurantDescription()
	{
        // Keep them, don't modify.
		String description = "";
		for(int i = 0; i < tables.size(); i++)
		{
			Table table = tables.get(i);
			description += ("Table: " + i + ", table size: " + table.getCapacity() + ", isAvailable: " + table.isAvailable() + ".");
			if(table.getCurrentOrder() == null)
				description += " No current order for this table"; 
			else
				description +=  " Order price: " + table.getCurrentOrder().getBill();
			
			description += ".\n";
		}
		description += "*****************************************\n";
		return description;
	}
}
```