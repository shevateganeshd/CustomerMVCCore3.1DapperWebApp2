# CustomerMVCCore3.1DapperWebApp2

**Startup.cs**

public void ConfigureServices(IServiceCollection services)
<br/>
{
<br/>
    //To includes both APIs and Razor Views 
    services.AddControllersWithViews();


    // Configure your database connection string here
    services.AddScoped<IDbConnection>(db => new SqlConnection(
        Configuration.GetConnectionString("DefaultConnection")));

    // Register the CustomerRepository
    services.AddScoped<CustomerRepository>();
}

<br/>

**CustomerRepository.cs**

public class CustomerRepository
<br/>
{
    private readonly IDbConnection _dbConnection;

    public CustomerRepository(IDbConnection dbConnection)
    {
        _dbConnection = dbConnection;
    }

    public async Task<IEnumerable<Customer>> GetAllCustomersAsync()
    {
        const string query = "SELECT * FROM Customer";
        return await _dbConnection.QueryAsync<Customer>(query);
    }

    public async Task<Customer> GetCustomerByIdAsync(int id)
    {
        const string query = "SELECT * FROM Customer WHERE Id = @Id";
        return await _dbConnection.QueryFirstOrDefaultAsync<Customer>(query, new { Id = id });
    }

    public async Task<int> AddCustomerAsync(Customer customer)
    {
        const string query = @"INSERT INTO Customer (FirstName, MiddleName, LastName, Address, Email, Phone) 
                    VALUES (@FirstName, @MiddleName, @LastName, @Address, @Email, @Phone)";
        return await _dbConnection.ExecuteAsync(query, customer);
    }

    public async Task<int> UpdateCustomerAsync(Customer customer)
    {
        const string query = @"UPDATE Customer 
                    SET FirstName = @FirstName, MiddleName=@MiddleName, LastName = @LastName, Address=@Address, Email=@Email, Phone=@Phone
                    WHERE Id = @Id";
        return await _dbConnection.ExecuteAsync(query, customer);
    }

    public async Task<int> DeleteCustomerAsync(int id)
    {
        const string query = "DELETE FROM Customer WHERE Id = @Id";
        return await _dbConnection.ExecuteAsync(query, new { Id = id });
    }
}
