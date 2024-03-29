import { useEffect, useState } from "react";

const CACHE_KEY = "coinlist";
const CACHE_EXPIRATION = 24 * 60 * 60 * 1000; // Cache expiration time (24 hours in milliseconds)

const Coinlist = () => {

 

    const [coinlist, setCoinlist] = useState([]);
    const [page, setPage] = useState(1);
    const [perPage, setPerPage] = useState(20);

    useEffect(() => {
        const fetchData = async () => {
            // Check if data exists in local storage/cache
            const cachedData = localStorage.getItem("coinlist");
            const cachedTimestamp = localStorage.getItem(`${CACHE_KEY}_timestamp`);
            const currentTime = new Date().getTime();

            if (cachedData && cachedTimestamp) {
                if (currentTime - cachedTimestamp <= CACHE_EXPIRATION) {
                    setCoinlist(JSON.parse(cachedData));
                }
            } else {
                const url = `https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=${perPage}&page=${page}&sparkline=false&locale=en`;

                const options = {
                    method: "GET",
                    headers: {
                        "X-RapidAPI-Key": '14e2617be1msh3c78f8e9c1f46e1p152996jsn972a5e39f901'
                        ,
                        "X-RapidAPI-Host": "coingecko.p.rapidapi.com",
                    },

                }
                try {
                    let res = await fetch(url);
                    const data = await res.json();

                    localStorage.setItem(CACHE_KEY, JSON.stringify(data));
                    localStorage.setItem(`${CACHE_KEY}_timestamp`, currentTime.toString());
                    console.log(data)
                    setCoinlist(data);
                } catch (err) {
                    console.log(err);
                }
            }
        }
            ;
        fetchData();
    }, [page, perPage]);

    const nextPage = () => {
        setPage((prevPage) => prevPage + 1);
    };

    const previousPage = () => {
        if (page > 1) {
            setPage((prevPage) => prevPage - 1);
        }
    };


    
    // Calculate the start and end indices for pagination
    const startIndex = (page - 1) * perPage;
    const endIndex = startIndex + perPage;
    const paginatedCoinlist = coinlist.slice(startIndex, endIndex);

    // Check if data returned is an array

    console.log("Page:", page);
    console.log("Start Index:", startIndex);
    console.log("End Index:", endIndex);
    console.log("Coinlist:", coinlist);





    return (
        <div >



            {/**dropdown menu */}
            <div className="border-2 border-blue-500 mt-2 ">

                <div>
                    <select className="m-2 py-4 px-4 outline-none font-serif text-lg font-semibold"
                        name="Category" id="crypto">
                        <option className="outline-none">Market Cap</option>
                        <option>Name</option>
                    </select>
                </div>
            </div>


            <div className="mt-2  mr-3">
                {/**Coin Display */}
                {paginatedCoinlist.map((coin) => (
                    <div key={coin.id}>
                        <div className="ml-3 border-l-2 border-r-2 mt-3 border-t-2 border-b-2
                         border-l-blue-500 py-3 px-2 border-r-blue-00 ">
                            <h1>Symbol: {coin.symbol}</h1>
                            <h1 className="text-blue-500">Name: {coin.name}</h1>
                        </div>
                    </div>
                ))}
            </div>

            <div className="pagination flex justify-around mt-4">
                <button className="border-1 border-red-500 rounded-md px-2 py-2 bg-blue-500"
                    onClick={previousPage} disabled={page === 1}>
                    Previous
                </button>
                <button className="border-1 border-red-500 rounded-md px-5 py-2 bg-blue-500"
                    onClick={nextPage}>Next</button>
            </div>
        </div>
    );
};

export default Coinlist;
