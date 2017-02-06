//# test                                      // 源文件路径 E:\\Java\demo\demo\Test.java
package demo; 								  

import java.util.*;
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

/. 声明三个布尔型变量来控制绘图程序的执行, 
声明并初始化五个ArrayList数组, 分别用于存
储每次frame.repaint()产生的参数x, y, wid
th, height, color ./

public class Test {
	boolean isDraw, isBack, isClear;		   			
	JFrame frame; 		   
	ArrayList<Integer> xList = new ArrayList<Integer>();
	ArrayList<Integer> yList = new ArrayList<Integer>();
	ArrayList<Integer> widthList = new ArrayList<Integer>();
	ArrayList<Integer> heightList = new ArrayList<Integer>();
	ArrayList<Color> colorList = new ArrayList<Color>();
  
	public static void main(String[] args) {
		Test t = new Test(); // 用new方法实例化Test对象(Test类在堆上的实体), 并将引用赋值给引用变量t
    	t.setUpGui();
	}
    // 窗口
	public void setUpGui() {
		frame = new JFrame("多按钮绘图");	  // 窗口标题
		JButton drawButton = new JButton("Draw");
    	JButton backButton = new JButton("Back");
    	JButton clearButton = new JButton("Clear");
    	JPanel panel = new JPanel();
    	panel.add(drawButton);                // 把三个按钮添加到JPanel面板上
    	panel.add(backButton);
    	panel.add(clearButton);
    	
    	drawButton.addActionListener(new DrawListener()); // 向三个按钮注册监听事件
    	backButton.addActionListener(new BackListener());
    	clearButton.addActionListener(new ClearListener());
    
    	frame.getContentPane().add(new DrawPanel());
    	frame.getContentPane().add(BorderLayout.SOUTH, panel);
    	frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    	frame.setSize(800, 600);
    	frame.setVisible(true);
	}
	// 由DrawListener类控制isDraw, 继而控制draw()方法实现画图操作
	public void draw(Graphics g) {
		 /* 因为每次拉伸窗口, 图形都会改变, 相当于重新执行一次
		 paintComponent()函数 , 所以这里用if语句设置为只有点击
		 按钮才能画图, else语句则是不让frame.repaint之后的图形
		 消失, 所以又重新画了一次 */
		if (isDraw) {
			int red = (int)(Math.random() * 255);
			int green = (int)(Math.random() * 255);
			int blue = (int)(Math.random() * 255);
			Color randColor = new Color(red, green, blue);
    
			int x = (int)(Math.random() * 101) + 100;
			int y = (int)(Math.random() * 101) + 100;
			int width = (int)(Math.random() * 101);
			int height = (int)(Math.random() * 101);
    
			xList.add(x);
			yList.add(y);
			widthList.add(width);
			heightList.add(height);
			colorList.add(randColor);
    
			for (int i = 0; i < xList.size(); i++) {
				g.setColor(colorList.get(i));
				g.fillRect(xList.get(i), yList.get(i), widthList.get(i), heightList.get(i));
			}
			isDraw = false;
		}
		else {
			for (int i = 0; i < xList.size(); i++) {
				g.setColor(colorList.get(i));
				g.fillRect(xList.get(i), yList.get(i), widthList.get(i), heightList.get(i));
			}
		}
	}
	// 由BackListener控制isBack, 继而控制back()方法实现撤销操作
	public void back(Graphics g) {
		if (isBack) {
			int endIndex = xList.size() - 1;
			if (endIndex >= 0) {          // 防止越界(下标/索引超出范围)
				g.clearRect(xList.get(endIndex), yList.get(endIndex), widthList.get(endIndex), heightList.get(endIndex));
				xList.remove(endIndex);
				yList.remove(endIndex);
				widthList.remove(endIndex);
				heightList.remove(endIndex);
				colorList.remove(endIndex);
				isBack = false;
			}
		}
	}
	// 由ClearListener类控制isClear, 继而控制clear()方法实现清屏操作
	public void clear(Graphics g) {
		if (isClear) {
			int arrayLength = xList.size();
			for (int i = 0; i < arrayLength; i++) {
				g.clearRect(xList.get(0), yList.get(0), widthList.get(0), heightList.get(0)); 
    
				xList.remove(0);
				yList.remove(0);
				widthList.remove(0);
				heightList.remove(0);
				colorList.remove(0);
			}
			isClear = false;
		}
	}
	// 系统自动执行的画图程序
	class DrawPanel extends JPanel {
		public void paintComponent(Graphics g) {
			draw(g);
			back(g);
			clear(g);
		}
	}
	// 画图的内部类, 实现(implement)接口的方法
	class DrawListener implements ActionListener {
		public void actionPerformed(ActionEvent event) {
			isDraw = true;
			frame.repaint();
		}
	}
    // 撤销的内部类
	class BackListener implements ActionListener {
		public void actionPerformed(ActionEvent event) {
			isBack = true;
			frame.repaint();
		}
	}
   // 清屏的内部类
	class ClearListener implements ActionListener {
		public void actionPerformed(ActionEvent event) {
			isClear = true;
			frame.repaint();
		}
	}
}
