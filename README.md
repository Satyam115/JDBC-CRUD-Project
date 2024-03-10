# JDBC-CRUD-Project
This is a jdbc crud operation for Student details

//Student
package edu.ty.studentapp.dto;

public class Student {
	private int id;
	private String name;
	private double marks;
	private long phoneNumber;
	private String emailId;
	private String gender;

	public Student() {
		super();
	}

	public Student(int id, String name, double marks, long phoneNumber, String emailId, String gender) {
		super();
		this.id = id;
		this.name = name;
		this.marks = marks;
		this.phoneNumber = phoneNumber;
		this.emailId = emailId;
		this.gender = gender;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public double getMarks() {
		return marks;
	}

	public void setMarks(double marks) {
		this.marks = marks;
	}

	public long getPhoneNumber() {
		return phoneNumber;
	}

	public void setPhoneNumber(long phoneNumber) {
		this.phoneNumber = phoneNumber;
	}

	public String getEmailId() {
		return emailId;
	}

	public void setEmailId(String emailId) {
		this.emailId = emailId;
	}

	public String getGender() {
		return gender;
	}

	public void setGender(String gender) {
		this.gender = gender;
	}

	@Override
	public String toString() {
		return "id=" + id + "\t, name=" + name + "\t, marks=" + marks + "\t, phoneNumber=" + phoneNumber
				+ "\t, emailId=" + emailId + "\t, gender=" + gender;
	}

}



//studentapp

package edu.ty.studentapp.servers;

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

import edu.ty.studentapp.dao.StudentDao;
import edu.ty.studentapp.dto.Student;

public class StudentApp {
	static {
		System.out.println("Welcome To Student App!!!");
	}

	public static void main(String[] args) {
		Scanner read = new Scanner(System.in);
		StudentDao dao = new StudentDao();
		while (true) {
			System.out.println();
			System.out.println("Select the choice:");
			System.out.println("1.Create(Insert)\n" + "2.Read All Student Info(Select)\n" + "3.Read Student With Id\n"
					+ "4.Update\n" + "5.Delete\n" + "6.Exit");
			int choice = read.nextInt();
			switch (choice) {
			case 1:
				System.out.println("Enter the Number of Student to Be Inserted:");
				int count = read.nextInt();
				List<Student> listOfStudent = new ArrayList<Student>();
				for (int i = 0; i < count; i++) {
					Student student = readStudentInfo(read);
					listOfStudent.add(student);
				}
				dao.saveStudent(listOfStudent);
				break;
			case 2:
				dao.displayAll();
				break;
			case 3:
				System.out.println("Enter the Student Id:");
				dao.findStudentById(read.nextInt());
				break;
			case 4:
				Student student2 = readStudentIdAndMarks(read);
				dao.updateStudentMarks(student2.getId(), student2.getMarks());
				break;
			case 5:
				System.out.println("Enter the Student Id:");
				dao.deleteStudent(read.nextInt());
				break;
			case 6:
				System.out.println("Thank You!!");
				System.exit(0);
				break;
			default:
				System.err.println("Invalied Choice!!!!!");
				break;
			}
		}
	}

	public static Student readStudentIdAndMarks(Scanner read) {
		Student student = new Student();
		System.out.println("Enter the Student Id:");
		student.setId(read.nextInt());
		System.out.println("Enter the Marks:");
		student.setMarks(read.nextDouble());
		return student;
	}

	public static Student readStudentInfo(Scanner read) {
		System.out.println("Enter the Student Id:");
		int id = read.nextInt();
		System.out.println("Enter the Student Name:");
		read.nextLine();
		String name = read.nextLine();
		System.out.println("Enter the Student Marks:");
		double marks = read.nextDouble();
		System.out.println("Enter the Student Phone Number");
		long phoneNumber = read.nextLong();
		System.out.println("Enter the Student EmailId:");
		String emailId = read.next();
		System.out.println("Enter the Student Gender");
		String gender = read.next();
		return new Student(id, name, marks, phoneNumber, emailId, gender);
	}

}



//DAO

package edu.ty.studentapp.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.List;

import edu.ty.studentapp.dto.Student;

public class StudentDao {
	static Connection connection;
	static Statement stmt;
	static PreparedStatement pstmt;
	static ResultSet resultSet;
	static {
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}

	public static Connection getConnection() throws SQLException {
		String dbUrl = "jdbc:mysql://localhost:3306";
		String userName = "root";
		String password = "root";
		return DriverManager.getConnection(dbUrl, userName, password);
	}

	public void saveStudent(Student student) {
		// Insert the Student info to table student
		try {
			connection = getConnection();
			String querry = "INSERT INTO jdbc_weje8.student VALUES (?,?,?,?,?,?)";
			pstmt = connection.prepareStatement(querry);
			pstmt.setInt(1, student.getId());
			pstmt.setString(2, student.getName());
			pstmt.setDouble(3, student.getMarks());
			pstmt.setLong(4, student.getPhoneNumber());
			pstmt.setString(5, student.getEmailId());
			pstmt.setString(6, student.getGender());
			if (pstmt.executeUpdate() == 1)
				System.out.println("Student Detalies Saved Successfully!");
			else
				System.err.println("Student Detalies Not Saved ");
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			if (connection != null) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			if (pstmt != null) {
				try {
					pstmt.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
	}

	public void displayAll() {
		// Display All Student info to user from the Student table
		try {
			connection = getConnection();
			String querry = "SELECT * FROM jdbc_weje8.student";
			stmt = connection.createStatement();
			resultSet = stmt.executeQuery(querry);
			System.out.println("Student Detalies Are:");
			while (resultSet.next()) {
				Student student = new Student();
				student.setId(resultSet.getInt(1));
				student.setName(resultSet.getString(2));
				student.setMarks(resultSet.getDouble(3));
				student.setPhoneNumber(resultSet.getLong(4));
				student.setEmailId(resultSet.getString(5));
				student.setGender(resultSet.getString(6));
				System.out.println(student);
			}

		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			if (connection != null) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			if (stmt != null) {
				try {
					stmt.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			if (resultSet != null) {
				try {
					resultSet.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
	}

	public void findStudentById(int id) {
		// Display the Student info Based on the Student id
		try {
			connection = getConnection();
			String querry = "SELECT * FROM jdbc_weje8.student WHERE id =?";
			pstmt = connection.prepareStatement(querry);
			pstmt.setInt(1, id);
			resultSet = pstmt.executeQuery();
			if (resultSet.next()) {
				System.out.println("Student Detalies:");
				Student student = new Student();
				student.setId(resultSet.getInt(1));
				student.setName(resultSet.getString(2));
				student.setMarks(resultSet.getDouble(3));
				student.setPhoneNumber(resultSet.getLong(4));
				student.setEmailId(resultSet.getString(5));
				student.setGender(resultSet.getString(6));
				System.out.println(student);
			} else
				System.err.println("Student With the Given Id = " + id + " Not Exists");

		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			if (connection != null) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			if (pstmt != null) {
				try {
					pstmt.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			if (resultSet != null) {
				try {
					resultSet.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}

	}

	public void updateStudentMarks(int id, double marks) {
		// Update the student marks using student id
		try {
			connection = getConnection();
			String querry = "UPDATE jdbc_weje8.student SET marks=? WHERE id=?";
			pstmt = connection.prepareStatement(querry);
			pstmt.setInt(2, id);
			pstmt.setDouble(1, marks);
			if (pstmt.executeUpdate() == 1)
				System.out.println("Student Detalies Updated Successfully!");
			else
				System.err.println("Student Detalies Not Updated ");
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			if (connection != null) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			if (pstmt != null) {
				try {
					pstmt.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
	}

	public void deleteStudent(int id) {
		// Delete the Student Based on the Student id
		try {
			connection = getConnection();
			String querry = "DELETE FROM jdbc_weje8.student WHERE id=?";
			pstmt = connection.prepareStatement(querry);
			pstmt.setInt(1, id);
			if (pstmt.executeUpdate() == 1)
				System.out.println("Student Detalies DELETED Successfully!");
			else
				System.err.println("Student Detalies Not DELETED ");
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			if (connection != null) {
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			if (pstmt != null) {
				try {
					pstmt.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
	}

	public void saveStudent(List<Student> listOfStudent) {
		for (Student student : listOfStudent) {
			// Insert the Student info to table student
			try {
				connection = getConnection();
				String querry = "INSERT INTO jdbc_weje8.student VALUES (?,?,?,?,?,?)";
				pstmt = connection.prepareStatement(querry);
				pstmt.setInt(1, student.getId());
				pstmt.setString(2, student.getName());
				pstmt.setDouble(3, student.getMarks());
				pstmt.setLong(4, student.getPhoneNumber());
				pstmt.setString(5, student.getEmailId());
				pstmt.setString(6, student.getGender());
				if (pstmt.executeUpdate() == 1)
					System.out.println("Student Detalies Saved Successfully!");
				else
					System.err.println("Student Detalies Not Saved ");
			} catch (SQLException e) {
				e.printStackTrace();
			} finally {
				if (connection != null) {
					try {
						connection.close();
					} catch (SQLException e) {
						e.printStackTrace();
					}
				}
				if (pstmt != null) {
					try {
						pstmt.close();
					} catch (SQLException e) {
						e.printStackTrace();
					}
				}
			}
		}
	}
}



