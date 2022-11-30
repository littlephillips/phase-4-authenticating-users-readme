# The flow will look like this:

     The user navigates to a login form on the React frontend.
     The user enters their username. There is no password (for now).
     The user submits the form, POSTing to /login on the Rails backend.
     In the create action of the SessionsController we set a cookie on the user's browser by writing their user ID into the session hash.
     Thereafter, the user is logged in. session[:user_id] will hold their user ID.

# SessionsController 
## login :rails
- to handle ourlogin route
- has one action `create` 
- post `"/login", to: "sessions#create"`

- `class SessionsController < appicationController
  def create
  user = User.find_by(username: params[username])
  session[:user_id] = user.id
  render json: user
  end
  end`

  ## react
`  function Login({ onLogin }) {
  const [username, setUsername] = useState("");
`
 ` function handleSubmit(e) {
    e.preventDefault();
    fetch("/login", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ username }),
    })
      .then((r) => r.json())
      .then((user) => onLogin(user));
  }
`
`  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={username}
        onChange={(e) => setUsername(e.target.value)}
      />
      <button type="submit">Login</button>
    </form>
  );
}
`
#
## staying logged in :rails
- handle reload
- has one action `show`
- get `"/me", to: "users#show"`
  
- `class UsersController < applicationController
def show
user = User.find_by(id: session[:user_id])
if user
    render json: user
else 
render json: { error: "Not authorized"}, status: :unauthorized
end
end
end`

## react
`function App() {
  const [user, setUser] = useState(null);
`
`  useEffect(() => {
    fetch("/me").then((response) => {
      if (response.ok) {
        response.json().then((user) => setUser(user));
      }
    });
  }, []);
`
 ` if (user) {
    return <h2>Welcome, {user.username}!</h2>;
  } else {
    return <Login onLogin={setUser} />;
  }
}`

#
## logout :rails
- has one action `logout`
- delete `"/logout" to: "sessions#destroy"`

- `def destroy sessions.delete 
  :user_id
  head :no_content
end`

## react
`  function Navbar({ onLogout }) {
  function handleLogout() {
    fetch("/logout", {
      method: "DELETE",
    }).then(() => onLogout());
  }`

`  return (
    <header>
      <button onClick={handleLogout}>Logout</button>
    </header>
  );
}`
#