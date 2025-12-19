import javax.swing.*;
import javax.swing.border.*;
import javax.swing.table.*;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;

class Book {
    private String id, title, author, publisher;

    public Book(String id, String title, String author, String publisher) {
        this.id = id;
        this.title = title;
        this.author = author;
        this.publisher = publisher;
    }

    public String getId() { return id; }
    public String getTitle() { return title; }
    public String getAuthor() { return author; }
    public String getPublisher() { return publisher; }

    public void setTitle(String t) { title = t; }
    public void setAuthor(String a) { author = a; }
    public void setPublisher(String p) { publisher = p; }
}

public class BookManagementSystemStyled extends JFrame {
    private ArrayList<Book> books = new ArrayList<>();
    private JTable table;
    private DefaultTableModel model;
    private JTextField idField, titleField, authorField, publisherField, searchField;

    public BookManagementSystemStyled() {
        setTitle("📘 Modern Book Management System");
        setSize(950, 650);
        setLocationRelativeTo(null);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        // 🌈 Header Panel
        JPanel headerPanel = new JPanel() {
            protected void paintComponent(Graphics g) {
                super.paintComponent(g);
                Graphics2D g2 = (Graphics2D) g;
                GradientPaint gp = new GradientPaint(
                        0, 0, new Color(0x007BFF),
                        getWidth(), getHeight(), new Color(0x80C8FF)
                );
                g2.setPaint(gp);
                g2.fillRect(0, 0, getWidth(), getHeight());
            }
        };
        headerPanel.setPreferredSize(new Dimension(950, 80));
        JLabel titleLabel = new JLabel("📚 Book Management Dashboard");
        titleLabel.setForeground(Color.WHITE);
        titleLabel.setFont(new Font("Segoe UI", Font.BOLD, 28));
        titleLabel.setHorizontalAlignment(SwingConstants.CENTER);
        headerPanel.setLayout(new BorderLayout());
        headerPanel.add(titleLabel, BorderLayout.CENTER);
        add(headerPanel, BorderLayout.NORTH);

        // 🧩 Main Panel
        JPanel mainPanel = new JPanel(new BorderLayout());
        mainPanel.setBackground(new Color(240, 247, 255));
        mainPanel.setBorder(new EmptyBorder(15, 15, 15, 15));
        add(mainPanel, BorderLayout.CENTER);

        // 📝 Input + Search Container (Top)
        JPanel topPanel = new JPanel(new BorderLayout(10, 10));
        topPanel.setBackground(Color.WHITE);

        // 🧠 Input Form
        JPanel formPanel = new JPanel(new GridLayout(2, 4, 10, 10));
        formPanel.setBackground(Color.WHITE);
        formPanel.setBorder(BorderFactory.createTitledBorder("Enter Book Details"));

        idField = new JTextField();
        titleField = new JTextField();
        authorField = new JTextField();
        publisherField = new JTextField();

        formPanel.add(new JLabel("Book ID:"));
        formPanel.add(idField);
        formPanel.add(new JLabel("Title:"));
        formPanel.add(titleField);
        formPanel.add(new JLabel("Author:"));
        formPanel.add(authorField);
        formPanel.add(new JLabel("Publisher:"));
        formPanel.add(publisherField);

        topPanel.add(formPanel, BorderLayout.CENTER);

        // 🔍 Search Panel (fixed placement)
        JPanel searchPanel = new JPanel(new FlowLayout(FlowLayout.LEFT, 10, 10));
        searchPanel.setBackground(Color.WHITE);
        searchPanel.setBorder(BorderFactory.createTitledBorder("Search Book by ID"));

        JLabel searchLabel = new JLabel("Enter Book ID:");
        searchField = new JTextField(15);
        JButton searchBtn = createStyledButton("🔍 Search");

        searchPanel.add(searchLabel);
        searchPanel.add(searchField);
        searchPanel.add(searchBtn);

        topPanel.add(searchPanel, BorderLayout.SOUTH); // ✅ placed properly under form

        mainPanel.add(topPanel, BorderLayout.NORTH);

        // 📋 Table
        String[] cols = {"Book ID", "Title", "Author", "Publisher"};
        model = new DefaultTableModel(cols, 0);
        table = new JTable(model);
        table.setRowHeight(26);
        table.setSelectionBackground(new Color(0x007BFF));
        table.setSelectionForeground(Color.WHITE);
        table.setFont(new Font("Segoe UI", Font.PLAIN, 14));

        JScrollPane scrollPane = new JScrollPane(table);
        scrollPane.setBorder(BorderFactory.createTitledBorder("Book Records"));
        mainPanel.add(scrollPane, BorderLayout.CENTER);

        // 🔘 Buttons
        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 20, 10));
        buttonPanel.setBackground(Color.WHITE);

        JButton addBtn = createStyledButton("+ Add");
        JButton updateBtn = createStyledButton("✏️ Update");
        JButton deleteBtn = createStyledButton("🗑 Delete");
        JButton clearBtn = createStyledButton("🧹 Clear");

        buttonPanel.add(addBtn);
        buttonPanel.add(updateBtn);
        buttonPanel.add(deleteBtn);
        buttonPanel.add(clearBtn);

        mainPanel.add(buttonPanel, BorderLayout.SOUTH);

        // 🧠 Listeners
        addBtn.addActionListener(e -> addBook());
        updateBtn.addActionListener(e -> updateBook());
        deleteBtn.addActionListener(e -> deleteBook());
        clearBtn.addActionListener(e -> clearFields());
        searchBtn.addActionListener(e -> searchBook());

        table.addMouseListener(new MouseAdapter() {
            public void mouseClicked(MouseEvent e) {
                int row = table.getSelectedRow();
                idField.setText(model.getValueAt(row, 0).toString());
                titleField.setText(model.getValueAt(row, 1).toString());
                authorField.setText(model.getValueAt(row, 2).toString());
                publisherField.setText(model.getValueAt(row, 3).toString());
            }
        });
    }

    // Button styling
    private JButton createStyledButton(String text) {
        JButton btn = new JButton(text);
        btn.setFont(new Font("Segoe UI", Font.BOLD, 14));
        btn.setForeground(Color.WHITE);
        btn.setBackground(new Color(0x007BFF));
        btn.setBorder(BorderFactory.createEmptyBorder(8, 20, 8, 20));
        btn.setCursor(new Cursor(Cursor.HAND_CURSOR));
        btn.setFocusPainted(false);

        btn.addMouseListener(new MouseAdapter() {
            public void mouseEntered(MouseEvent evt) {
                btn.setBackground(new Color(0x0056B3));
            }
            public void mouseExited(MouseEvent evt) {
                btn.setBackground(new Color(0x007BFF));
            }
        });
        return btn;
    }

    // Add Book
    private void addBook() {
        String id = idField.getText(), title = titleField.getText(),
                author = authorField.getText(), publisher = publisherField.getText();

        if (id.isEmpty() || title.isEmpty() || author.isEmpty() || publisher.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Please fill all fields!", "⚠ Warning", JOptionPane.WARNING_MESSAGE);
            return;
        }

        for (Book b : books)
            if (b.getId().equals(id)) {
                JOptionPane.showMessageDialog(this, "Book ID already exists!", "❌ Error", JOptionPane.ERROR_MESSAGE);
                return;
            }

        books.add(new Book(id, title, author, publisher));
        model.addRow(new Object[]{id, title, author, publisher});
        clearFields();
        JOptionPane.showMessageDialog(this, "✅ Book added successfully!");
    }

    // ✏️ Update Book
    private void updateBook() {
        int row = table.getSelectedRow();
        if (row < 0) {
            JOptionPane.showMessageDialog(this, "Select a book to update!", "⚠ Warning", JOptionPane.WARNING_MESSAGE);
            return;
        }
        String title = titleField.getText(), author = authorField.getText(), publisher = publisherField.getText();

        books.get(row).setTitle(title);
        books.get(row).setAuthor(author);
        books.get(row).setPublisher(publisher);

        model.setValueAt(title, row, 1);
        model.setValueAt(author, row, 2);
        model.setValueAt(publisher, row, 3);
        JOptionPane.showMessageDialog(this, "✏️ Book updated successfully!");
        clearFields();
    }

    // 🗑 Delete Book
    private void deleteBook() {
        int row = table.getSelectedRow();
        if (row < 0) {
            JOptionPane.showMessageDialog(this, "Select a book to delete!", "⚠ Warning", JOptionPane.WARNING_MESSAGE);
            return;
        }
        books.remove(row);
        model.removeRow(row);
        clearFields();
        JOptionPane.showMessageDialog(this, "🗑 Book deleted successfully!");
    }

    // 🧹 Clear Fields
    private void clearFields() {
        idField.setText("");
        titleField.setText("");
        authorField.setText("");
        publisherField.setText("");
        table.clearSelection();
    }

    // 🔍 Search Book by ID
    private void searchBook() {
        String searchId = searchField.getText().trim();
        if (searchId.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Enter a Book ID to search!", "⚠ Warning", JOptionPane.WARNING_MESSAGE);
            return;
        }

        for (int i = 0; i < books.size(); i++) {
            Book b = books.get(i);
            if (b.getId().equalsIgnoreCase(searchId)) {
                table.setRowSelectionInterval(i, i); // highlight found row
                JOptionPane.showMessageDialog(this,
                        "📘 Book Found:\n\nID: " + b.getId() +
                                "\nTitle: " + b.getTitle() +
                                "\nAuthor: " + b.getAuthor() +
                                "\nPublisher: " + b.getPublisher(),
                        "✅ Search Result", JOptionPane.INFORMATION_MESSAGE);
                return;
            }
        }
        JOptionPane.showMessageDialog(this, "❌ No book found with ID: " + searchId, "Not Found", JOptionPane.ERROR_MESSAGE);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new BookManagementSystemStyled().setVisible(true));
    }
}

# library-management-in-oots
library management system in object oriented techniques using java
