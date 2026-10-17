# Index

- [Intro](#intro)


# Intro

```jsx
import React, { useEffect } from 'react';
import { createRoot } from 'react-dom/client';
import { Provider, useDispatch, useSelector } from 'react-redux';
import { configureStore, createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import axios from 'axios';

// ========================
// 📦 Async Thunk (API Call)
// ========================
const fetchUsers = createAsyncThunk('users/fetchUsers', async () => {
  const response = await axios.get('https://jsonplaceholder.typicode.com/users');
  return response.data; // returns an array of users
});

// ==========================
// 🧠 Create Slice (State + Reducers)
// ==========================
const userSlice = createSlice({
  name: 'users',
  initialState: {
    list: [],
    loading: false,
    error: null,
  },
  reducers: {
    // Local reducer: manually add a user
    addUser: (state, action) => {
      const newUser = {
        id: Date.now(),
        name: action.payload.name,
        email: action.payload.email,
      };
      state.list.push(newUser);
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchUsers.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchUsers.fulfilled, (state, action) => {
        state.loading = false;
        state.list = action.payload;
      })
      .addCase(fetchUsers.rejected, (state) => {
        state.loading = false;
        state.error = 'Failed to fetch users';
      });
  },
});

const { addUser } = userSlice.actions; // export local action

// ====================
// 🏪 Redux Store Setup
// ====================
const store = configureStore({
  reducer: {
    users: userSlice.reducer,
  },
});

// ============================
// 🧩 React Component (Consumer)
// ============================
const UserList = () => {
  const dispatch = useDispatch();
  const { list, loading, error } = useSelector((state) => state.users);

  useEffect(() => {
    dispatch(fetchUsers()); // fetch users on mount
  }, [dispatch]);

  const handleAdd = () => {
    const name = prompt('Enter name:');
    const email = prompt('Enter email:');
    if (name && email) {
      dispatch(addUser({ name, email })); // use local reducer
    }
  };

  return (
    <div style={{ padding: '20px', fontFamily: 'sans-serif' }}>
      <h2>👥 User List</h2>

      <button onClick={handleAdd}>➕ Add User</button>

      {loading && <p>Loading...</p>}
      {error && <p style={{ color: 'red' }}>{error}</p>}

      <ul>
        {list.map((user) => (
          <li key={user.id}>
            {user.name} (<i>{user.email}</i>)
          </li>
        ))}
      </ul>
    </div>
  );
};
```

- Redux vs Context API- Redux is useful for large apps with complex state management and where there are multiple or frequently changing global states (prevents unnecessary re renders)
