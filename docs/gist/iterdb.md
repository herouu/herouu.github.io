```java
import cn.hutool.core.bean.BeanUtil;
import cn.hutool.core.collection.CollUtil;
import cn.hutool.core.collection.IterUtil;
import cn.hutool.core.collection.ListUtil;
import cn.hutool.core.convert.Convert;
import cn.hutool.core.lang.func.VoidFunc1;
import cn.hutool.core.util.NumberUtil;
import com.github.pagehelper.PageHelper;
import com.google.common.collect.AbstractIterator;

import java.util.List;
import java.util.Objects;


public class IterDBUtil {


    private static final Integer ONE_THOUSAND = 1000;

    /**
     * DEFAULT_FIELD
     */
    private static final String DEFAULT_FIELD = "id";

    /**
     * exec
     *
     * @param mode      mode
     * @param DBFunc  loadFunc
     * @param voidFunc1 voidFunc1
     * @param fieldName fieldName
     * @param <T>       T
     */
    public static <T> void exec(Mode mode, DBFunc<T> DBFunc, VoidFunc1<List<T>> voidFunc1, String fieldName) {
        exec(mode, ONE_THOUSAND, DBFunc, voidFunc1, fieldName);
    }


    /**
     * exec
     *
     * @param mode      mode
     * @param limit     limit
     * @param DBFunc  loadFunc
     * @param voidFunc1 voidFunc1
     * @param <T>       T
     */
    public static <T> void exec(Mode mode, Integer limit, DBFunc<T> DBFunc, VoidFunc1<List<T>> voidFunc1) {
        exec(mode, limit, DBFunc, voidFunc1, DEFAULT_FIELD);
    }

    /**
     * exec
     *
     * @param mode      mode
     * @param DBFunc  loadFunc
     * @param voidFunc1 voidFunc1
     * @param <T>       T
     */
    public static <T> void exec(Mode mode, DBFunc<T> DBFunc, VoidFunc1<List<T>> voidFunc1) {
        exec(mode, DBFunc, voidFunc1, DEFAULT_FIELD);
    }

    /**
     * exec
     *
     * @param DBFunc  loadFunc
     * @param voidFunc1 voidFunc1
     * @param <T>       T
     */
    public static <T> void exec(DBFunc<T> DBFunc, VoidFunc1<List<T>> voidFunc1) {
        exec(Mode.page, DBFunc, voidFunc1, DEFAULT_FIELD);
    }


    /**
     * exec
     *
     * @param mode      mode
     * @param limit     limit
     * @param DBFunc  loadFunc
     * @param voidFunc1 voidFunc1
     * @param fieldName fieldName
     * @param <T>       T
     */
    public static <T> void exec(Mode mode, int limit, DBFunc<T> DBFunc, VoidFunc1<List<T>> voidFunc1, String fieldName) {
        DbIter<T> tDbIter = new DbIter<>(mode, limit, DBFunc, fieldName);
        List<T> t = ListUtil.list(false);
        IterUtil.forEach(tDbIter, item -> CollUtil.addAll(t, item));
        List<List<T>> partition = ListUtil.partition(t, limit);
        IterUtil.forEach(IterUtil.getIter(partition),
                item -> {
                    if (CollUtil.isNotEmpty(item)) {
                        voidFunc1.callWithRuntimeException(item);
                    }
                });
    }


    /**
     * 数据库迭代器
     *
     *
     */
    private static class DbIter<T> extends AbstractIterator<List<T>> {


        /**
         * loadFunc
         */
        private final DBFunc<T> DBFunc;


        /**
         * mode
         */
        private final Mode mode;


        /**
         * offset
         */
        private int offset = 0;


        /**
         * maxId
         */
        private Long maxId;


        /**
         * fieldName
         */
        private String fieldName;

        /**
         * limit
         */
        private final int limit;


        /**
         * stop
         */
        private boolean stop = false;


        /**
         * DbIterator
         *
         * @param mode      mode
         * @param limit     limit
         * @param DBFunc  loadFunc
         * @param fieldName fieldName
         */
        public DbIter(Mode mode, int limit, DBFunc<T> DBFunc, String fieldName) {
            this.DBFunc = DBFunc;
            this.mode = mode;
            this.limit = limit;
            this.fieldName = fieldName;
        }

        @Override
        protected List<T> computeNext() {
            if (stop) {
                return endOfData();
            }
            List<T> load;

            if (mode == Mode.none) {
                load = loadFunc.load(maxId);
                this.stop = true;
                return load;
            }

            try {
                PageHelper.offsetPage(mode == Mode.page ? offset : 0, limit, false);
                load = loadFunc.load(maxId);
            } finally {
                PageHelper.clearPage();
            }
            if (CollUtil.size(load) < limit) {
                this.stop = true;
                return load;
            }
            if (mode == Mode.page) {
                offset = offset + limit;
            } else {
                maxId = maxId(load);
            }
            return load;
        }


        /**
         * maxId
         *
         * @param list list
         * @return Long
         */
        private Long maxId(List<T> list) {
            Long id = null;
            for (Object t : list) {
                Long aLong;
                if (t instanceof Long) {
                    aLong = Convert.toLong(t);
                } else if (BeanUtil.isBean(t.getClass())) {
                    aLong = Convert.toLong(BeanUtil.getProperty(t, fieldName));
                } else {
                    throw new IllegalArgumentException("不支持的数据类型");
                }
                if (Objects.isNull(id)) {
                    id = aLong;
                    continue;
                }
                if (NumberUtil.compare(id, aLong) < 0) {
                    id = aLong;
                }
            }
            return id;
        }
    }

    /**
     * 数据加载func
     *
     *
     */
    @FunctionalInterface
    public interface DBFunc<T> {
        /**
         * 加载数据
         *
         * @param id id
         * @return thelist
         */
        List<T> load(Long id);
    }


    /**
     * 迭代器模式 scroll-滚动分页 page-普通分页
     *
     *
     */
    public enum Mode {
        /**
         * scroll
         */
        scroll,
        /**
         * page
         */
        page,
        /**
         * none
         */
        none,
    }
}
```
