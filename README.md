//Department-crud

import React, { useState } from 'react';
import { Modal, Button,  Dropdown , DropdownButton } from 'react-bootstrap';
import 'bootstrap/dist/css/bootstrap.min.css';
import './App.css';

function App() {
  const [data, setData] = useState([]);
  const[empname , setEmpName] = useState('');
  const [depName, setDepName] = useState('');
  const [group, setGroup] = useState('');
  const [id, setId] = useState(0);
  const [isUpdate, setIsUpdate] = useState(false);
  const [IDCounter, setIDCounter] = useState(1);
  const [depCodeCounter, setDepCodeCounter] = useState(1);
  const [showModal, setShowModal] = useState(false);
  const [selectedItem, setSelectedItem] = useState(null);

  const groups = [
    { name: "Product", code: "PDG" },
    { name: "Quality", code: "QA*" },
    { name: "Human Resource", code: "HR*" },
    { name: "Performance", code: "PER" }
  ];  

  const handleEdit = (id) => {
    const item = data.find(item => item.id === id);
    if (item) {
      setIsUpdate(true);
      setId(id);
      setDepName(item.depName || '');
      setGroup(item.group || '');
    }
  };

  const handleDelete = (id) => {
    if (window.confirm("Are you sure you want to delete this item?")) {
      const updatedData = data.filter(item => item.id !== id);
      setData(updatedData);
    }
  };

  const handleDetails = (id) => {
    const item = data.find(item => item.id === id);
    if (item) {
      setShowModal(true);
      setSelectedItem(item);
    }
  };

  const handleSave = (e) => {
    e.preventDefault();
    let error = '';

    const nameRegex = /^[a-zA-Z ]+$/;

    if (!depName.match(nameRegex))
      error += 'Department name is required and should contain only alphabets, ';

    if (data.some(item => item.depName === depName && item.group === group)) {
      error += 'The same group name cannot be used again in the same department.';
    }

    if (error === '') {
      const newObject = {
        id: IDCounter,
        depName,
        group,
        depcode: `Dep@${depCodeCounter}`
      };
      setData([...data, newObject]);
      setIDCounter(IDCounter + 1);
      setDepCodeCounter(depCodeCounter + 1);
      handleClear();
    } else {
      alert(error);
    }
  };

  const handleUpdate = () => {
    let error = '';

    if (data.some(item => item.id !== id && item.depName === depName && item.group === group)) {
      error += 'The same group name cannot be used again in the same department.';
    }

    if (error === '') {
      const index = data.findIndex(item => item.id === id);
      const updatedData = [...data];
      updatedData[index] = { ...updatedData[index], depName, group };
      setData(updatedData);
      handleClear();
    } else {
      alert(error);
    }
  };

  const handleClear = () => {
    setId(0);
    setDepName('');
    setGroup('');
    setIsUpdate(false);
  };

  return (
    <div className="App"> 
      <h1>Custom Fields</h1>
      <div className="form-container">
        <label>
          Employee Name:
          <input type='text' placeholder='Enter your name' onChange={(e) => setEmpName(e.target.value)} value={empname}/>
        </label>
        <label>
          Department Name:
          <input type='text' placeholder='Enter Department Name' onChange={(e) => setDepName(e.target.value)} value={depName} />
        </label>
        <label>
          Department Group:
          <select value={group} onChange={(e) => setGroup(e.target.value)}>
            <option value = "" disabled>Select Group</option>
            {groups.map((groupItem,index) => (
              <option key={index} value={groupItem.name}>{groupItem.name}</option>
            ))}
          </select>
        </label>
        <div className="form-buttons">
          {
            !isUpdate ?
              <button className='btn btn-primary' onClick={(e) => handleSave(e)}>Save</button>
              :
              <button className='btn btn-primary' onClick={handleUpdate}>Update</button>
          }
          <button className='btn btn-danger' onClick={handleClear}>Clear</button>
        </div>
      </div>

      <table className='table table-hover'>
        <thead>
          <tr>
            <td>Field ID</td>
            <td>ID</td>
            <td>Department Code</td>
            <td>Department Name</td>
            <td>Group</td>
            <td>Actions</td>
          </tr> 
        </thead>
        <tbody>
          {
            data.map((item, index) => (
              <React.Fragment key={index}>
                <tr>
                  <td>{index + 1}</td>
                  <td>{item.id}</td>
                  
                  <td>{item.depcode}</td>
                  <td>{item.depName}</td>
                  <td>{item.group}</td>
                  <td>
                    <DropdownButton id="dropdown-basic-button" title="⋮">
                      <Dropdown.Item onClick={() => handleEdit(item.id)}>Edit</Dropdown.Item>
                      <Dropdown.Item onClick={() => handleDelete(item.id)}>Delete</Dropdown.Item>
                      <Dropdown.Item onClick={() => handleDetails(item.id)}>Details</Dropdown.Item>
                    </DropdownButton>
                  </td>
                </tr>
              </React.Fragment>
            ))
          }
        </tbody>
      </table>
      <Modal show={showModal} onHide={() => setShowModal(false)}>
        <Modal.Header closeButton>
          <Modal.Title>Department Details</Modal.Title>
        </Modal.Header>
        <Modal.Body>
          {selectedItem && (
            <div>
              <p><strong>Department Code:</strong> {selectedItem.depcode}</p>
              <p><strong>Department Name:</strong> {selectedItem.depName}</p>
              <p><strong>Group:</strong> {selectedItem.group}</p>
            </div>
          )}
        </Modal.Body>
      </Modal>
    </div>
  );
}

export default App;
