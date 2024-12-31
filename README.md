# TerraLogic
import React, { useState } from 'react';
import { View, Text, TextInput, Button, FlatList, StyleSheet, Image, ActivityIndicator } from 'react-native';
import axios from 'axios';

export default function App() {
  const [userName, setUserName] = useState('');
  const [userDetails, setUserDetails] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  const handleSearch = async () => {
    if (userName.trim() === '') {
      setError('Please enter a username');
      return;
    }

    setLoading(true);
    setError('');
    try {
      // Call the GitHub API with the search query
      const response = await axios.get(`https://api.github.com/search/users?q=${userName}`);
      setUserDetails(response.data.items); // Set the user details from the response
    } catch (err) {
      setError('Error fetching data');
    } finally {
      setLoading(false);
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.header}>GitHub User Search</Text>
      
      {/* Input field for username */}
      <TextInput
        style={styles.input}
        placeholder="Enter GitHub username"
        value={userName}
        onChangeText={setUserName}
      />
      
      {/* Submit button to trigger search */}
      <Button title="Search" onPress={handleSearch} />

      {/* Display error if any */}
      {error ? <Text style={styles.error}>{error}</Text> : null}

      {/* Show loading indicator while fetching data */}
      {loading ? <ActivityIndicator size="large" color="#0000ff" style={styles.loader} /> : null}

      {/* Show user details in a list */}
      <FlatList
        data={userDetails}
        keyExtractor={(item) => item.id.toString()}
        renderItem={({ item }) => (
          <View style={styles.userCard}>
            <Image source={{ uri: item.avatar_url }} style={styles.avatar} />
            <Text style={styles.userName}>{item.login}</Text>
            <Text style={styles.userInfo}>ID: {item.id}</Text>
            <Text style={styles.userInfo}>URL: {item.html_url}</Text>
          </View>
        )}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 16,
    backgroundColor: '#f5f5f5',
  },
  header: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 20,
  },
  input: {
    height: 40,
    borderColor: 'gray',
    borderWidth: 1,
    borderRadius: 8,
    paddingHorizontal: 10,
    marginBottom: 20,
    width: '100%',
  },
  error: {
    color: 'red',
    marginBottom: 10,
  },
  loader: {
    marginBottom: 20,
  },
  userCard: {
    backgroundColor: '#fff',
    padding: 10,
    marginBottom: 15,
    borderRadius: 8,
    width: '100%',
    alignItems: 'center',
    borderWidth: 1,
    borderColor: '#ddd',
  },
  avatar: {
    width: 80,
    height: 80,
    borderRadius: 40,
    marginBottom: 10,
  },
  userName: {
    fontSize: 18,
    fontWeight: 'bold',
  },
  userInfo: {
    fontSize: 14,
    color: '#555',
  },
});
