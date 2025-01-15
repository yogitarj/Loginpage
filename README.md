package com.info;

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.sql.*;

public class LoginPage extends JFrame {

    /**
	 * 
	 */
	private static final long serialVersionUID = 1L;
	private JTextField txtUsername;
    private JPasswordField txtPassword;
    private JButton btnLogin;
    private JLabel lblMessage;

    // Constructor to setup the GUI
    public LoginPage() {
        setTitle("Blood Bank Management System - Login");
        setLayout(new GridLayout(4, 2));
        setSize(400, 200);
        setLocationRelativeTo(null);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JLabel lblUsername = new JLabel("Username:");
        txtUsername = new JTextField();
        add(lblUsername);
        add(txtUsername);

        JLabel lblPassword = new JLabel("Password:");
        txtPassword = new JPasswordField();
        add(lblPassword);
        add(txtPassword);

        btnLogin = new JButton("Login");
        add(btnLogin);

        lblMessage = new JLabel("");
        lblMessage.setForeground(Color.RED);
        add(lblMessage);

        btnLogin.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                validateLogin();
            }
        });
    }

    // Method to validate login credentials
    private void validateLogin() {
        String username = txtUsername.getText();
        String password = new String(txtPassword.getPassword());

        if (username.isEmpty() || password.isEmpty()) {
            lblMessage.setText("Please fill in both fields.");
            return;
        }

        try (Connection conn= DriverManager.getConnection("jdbc:mysql://localhost:3306/blood_bank", "root", "Jahagirdar@1");

             PreparedStatement stmt = conn.prepareStatement("SELECT * FROM users WHERE username = ? AND password = ?")) {

            stmt.setString(1, username);
            stmt.setString(2, password);

            ResultSet rs = stmt.executeQuery();
            if (rs.next()) {
                String role = rs.getString("role");
                lblMessage.setText("Login successful as " + role);
                lblMessage.setForeground(Color.GREEN);
                JOptionPane.showMessageDialog(this, "Welcome " + role, "Login Success", JOptionPane.INFORMATION_MESSAGE);
                System.exit(0);  // Exit after successful login
            } else {
                lblMessage.setText("Invalid username or password.");
                lblMessage.setForeground(Color.RED);
            }
        } catch (SQLException ex) {
            ex.printStackTrace();
            lblMessage.setText("Database connection error.");
        }
    }

    // Main method to launch the application
    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                new LoginPage().setVisible(true);
            }
        });
    }
}
