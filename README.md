
import React, { useEffect, useState } from 'react';
import millify from 'millify';
import { Link } from 'react-router-dom';
import { Card, Row, Col, Input, Table, Tag } from 'antd';
import { useGetCryptosQuery } from '../services/cryptoApi';
import Loader from './Loader';

const Cryptocurrencies = ({ simplified }) => {
  const count = simplified ? 10 : 100;
  const { data: cryptosList, isFetching } = useGetCryptosQuery(count);
  const [cryptos, setCryptos] = useState();
  const [searchTerm, setSearchTerm] = useState('');
  const number = parseFloat(text);
  const formattedNumber = number.toFixed(2);
  
  useEffect(() => {
    setCryptos(cryptosList?.data?.coins);

    const filteredData = cryptosList?.data?.coins.filter((item) =>
      item.name.toLowerCase().includes(searchTerm)
    );

    setCryptos(filteredData);
  }, [cryptosList, searchTerm]);

  const columns = [
    {
      title: 'Name',
      dataIndex: 'name',
      key: 'name',
      render: (text, record) => (
        <Link to={`/crypto/${record.uuid}`}>{text}</Link>
      ),
    },
    {
      title: 'Price',
      dataIndex: 'price',
      key: 'price',
      render: (text) => <>{millify(text)}</>,
    },
    {
      title: 'Market Cap',
      dataIndex: 'marketCap',
      key: 'marketCap',
      render: (text) => <>{millify(text)}</>,
    },
    {
      title: '24h Change',
      dataIndex: 'change',
      key: 'change',
      render: (text) => (
        <Tag color={text >= 0 ? 'green' : 'red'}>{text?.toFixed(2)}%</Tag>
      ),
    },
  ];

  if (isFetching) return <Loader />;

  return (
    <>
      {!simplified && (
        <div className="search-crypto">
          <Input
            placeholder="Search Cryptocurrency"
            onChange={(e) => setSearchTerm(e.target.value.toLowerCase())}
          />
        </div>
      )}
      <Table
        columns={columns}
        dataSource={cryptos}
        pagination={{ defaultPageSize: 10 }}
      />
    </>
  );
};

export default Cryptocurrencies;
