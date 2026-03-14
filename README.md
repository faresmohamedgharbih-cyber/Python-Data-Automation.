import sqlite3

# إنشاء قاعدة بيانات وجداول المبيعات
def setup_database():
    conn = sqlite3.connect('ecommerce.db')
    cursor = conn.cursor()
    
    # إنشاء جدول العملاء والطلبات (One-to-Many Relationship)
    cursor.execute('''CREATE TABLE IF NOT EXISTS customers 
                      (id INTEGER PRIMARY KEY, name TEXT, email TEXT)''')
    
    cursor.execute('''CREATE TABLE IF NOT EXISTS orders 
                      (id INTEGER PRIMARY KEY, customer_id INTEGER, 
                       amount REAL, date TEXT,
                       FOREIGN KEY(customer_id) REFERENCES customers(id))''')
    
    print("Database Schema Created Successfully!")
    conn.commit()
    conn.close()

# استعلام SQL معقد لاستخراج تقرير المبيعات لكل عميل (JOIN)
def get_sales_report():
    conn = sqlite3.connect('ecommerce.db')
    query = '''
    SELECT customers.name, SUM(orders.amount) as total_spent
    FROM customers
    JOIN orders ON customers.id = orders.customer_id
    GROUP BY customers.name
    ORDER BY total_spent DESC
    '''
    df_report = pd.read_sql_query(query, conn)
    conn.close()
    return df_report

setup_database()
