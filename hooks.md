### useAPIRequest
#### Demo https://codesandbox.io/s/useapirequest-287p6
#### useAPIRequest.js
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
### usePrefetch
#### Demo: https://codesandbox.io/s/useprefetch-qixiu?file=/src/usePrefetch.js
#### usePrefetch.js
```js
import { useEffect, useRef, useCallback } from "react";

export const PREFETCH_DELAY = 500;

const usePrefetch = (
  requestFn,
  preFetchCondition = true,
  delay = PREFETCH_DELAY
) => {
  const timeoutID = useRef(null);
  // We must store this in a ref so the handler inside setTimeout can "see" the current value.
  const prefetchConditionRef = useRef(preFetchCondition);
  // Update the ref when the values change.
  useEffect(() => {
    prefetchConditionRef.current = preFetchCondition;
  }, [preFetchCondition]);

  const makeRequest = () => {
    timeoutID.current = window.setTimeout(() => {
      if (prefetchConditionRef.current && requestFn) {
        requestFn();
      }
    }, delay);
  };
  const cancelRequest = useCallback(() => {
    if (timeoutID.current) {
      window.clearTimeout(timeoutID.current);
    }
  }, []);

  const handlers = {
    onMouseEnter: makeRequest,
    onFocus: makeRequest,
    onMouseLeave: cancelRequest,
    onBlur: cancelRequest
  };

  return { makeRequest, cancelRequest, handlers };
};

export default usePrefetch;

```
