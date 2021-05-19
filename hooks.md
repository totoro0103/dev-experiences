### useAPIRequest
```js
import { useEffect, useState, useRef } from "react";
import produce from "immer";

const useAPIRequest = (request, initialData = null, deps = []) => {
  const [data, setData] = useState(initialData);
  const [error, setError] = useState(undefined);
  const [loading, setLoading] = useState(false);
  const [lastUpdated, setLastUpdated] = useState(0);
  const mounted = useRef(true);

  const _request = () => {
    if (!request) {
      return;
    }

    setLoading(true);
    setError(undefined);
    request()
      .then((responseData) => {
        if (!mounted) {
          return;
        }
        setLoading(false);
        setLastUpdated(Date.now());
        setData(responseData.data);
      })
      .catch((err) => {
        if (!mounted) {
          return;
        }
        setLoading(false);
        setError(err);
      });
  };
  useEffect(() => {
    _request();
    return () => {
      mounted.current = false;
    };
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, deps);

  const transformData = (data) => {
    setData(produce(data));
  };
  return { data, loading, lastUpdated, error, update: _request, transformData };
};

export default useAPIRequest; 
```
